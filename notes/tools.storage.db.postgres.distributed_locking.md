---
id: v40vzle7uhe3lz838qohywd
title: distributed_locking
desc: ''
updated: 1730526634372
created: 1730525473844
---
https://medium.com/@darinmcgill/distributed-locking-and-error-tracking-for-python-with-postgres-9ecc7b916a63

#### For working on one machine:

- This uses the mechanism of opening a file in exclusive mode. Only one process can do so at any one time 
``` py
class CantLock(Exception):
    pass
class MachineLock(object):
    LOCK_DIRECTORY = "/srv/lock/"  # adjust to taste
    def __init__(self, file_name):
        self.file_path = LOCK_DIRECTORY + file_name + ".lock"
    def __enter__(self):
        try:
            self.fd = os.open(self.file_path, 
                              os.O_CREAT | os.O_EXCL | os.O_RDWR)
        except Exception as e:
            raise CantLock(e)
   def __exit__(self, a_type, value, traceback1):
        os.close(self.fd)
        os.unlink(self.file_path)
```

#### For muliple machines


It occured to me that there’s a simpler mechanism for establishing exclusivity: insertion into a unique index. If you attempt to insert into a unique index and an entry is already there then you’ll get an error, but if the insert suceeds then you know you have the lock. Releasing in __exit__ would then just amount do removing the corresponding row.

The devil is in the details of course. What happens when a python process is stopped and doesn’t have the chance to get to the __exit__ ? This happens when a python process receives a SIGKILL, or the docker container its running in gets redeployed, or someone unplugs the machine.

I initially experimented with automatic lock expiry. The idea is that you’d say at lock creation time how long you want it for, and then it would automatically be released (by someone else trying to acquire the same lock) past expiry. This works okay if you know ahead of time how long you want to hold the lock for, or want to implement an automatic lock refresh system, but both of those two approches end up with lots of unwanted complexity.


The simpler solution I came to involves using the existance of the locker’s database connection as a proxy for the locker still being alive and wanting to maintain the lock. So if the locker has disconnected, other lockers can assume they’re dead and don’t need the lock anymore. The corresponding table and stored procedure to acquire a lock looks something like:

``` sql
create table locks (
    id UUID PRIMARY KEY DEFAULT pgcrypto.gen_random_uuid(),
    named TEXT UNIQUE NOT NULL,
    pid INTEGER NOT NULL,
    ins TIMESTAMPTZ NOT NULL DEFAULT now()
);
create or replace function "acquire"(x text) returns void
    volatile
    security definer
as $dbvis$
DECLARE
    lock_id UUID;
BEGIN
delete from locks
where tracking in (
    select l.tracking
    from locks as l
    left join pg_stat_activity as p on p.pid = l.pid
    where p.pid is null
);
insert into locks(named, pid) 
values (x, pg_backend_pid()) returning tracking into lock_id;
return lock_id;
END;
$dbvis$ LANGUAGE plpgsql
```

To get this lock you’d just put `select acquire('lock-name')`; inside of your __enter__ method of your lock function. Release the lock with something like `delete from locks where id='%(lock_id)s'`;

That gives you a functional solution, but misses the opportunity to do something more, namely to kill two birds with one stone and track failed tasks using the same syntax. Python’s __exit__ method receives information about any exceptions escaping the with block, and this can be valuable information to have in a centralized place. To capture this, I call a database function from __exit__ that saves exception information (if present) in addition to releasing the lock:

``` sql
CREATE TABLE released (
    id UUID PRIMARY KEY,
    named TEXT NOT NULL,
    locked TIMESTAMPTZ,
    released TIMESTAMPTZ DEFAULT now(),
    problem TEXT,
    details TEXT
);
CREATE OR REPLACE FUNCTION "acquit"(lock_id UUID, error_type TEXT DEFAULT NULL::TEXT, error_details TEXT DEFAULT NULL::TEXT) RETURNS VOID VOLATILE SECURITY DEFINER as $dbvis$
BEGIN
    INSERT INTO released (id, named, locked, problem, details)
        SELECT id, named, ins, error_type, error_details
        FROM locks WHERE id = lock_id
    ON CONFLICT DO NOTHING;
    DELETE FROM LOCKS WHERE id = lock_id;
END; $dbvis$ LANGUAGE plpgsql
Additionally, I’ve altered the acquire function to note that a disconnect occured when a locker tries to acquire a lock from a defunct process:

...
INSERT INTO released (id, locked, named, problem)
    SELECT l.id, ins, l.named, 'disconnected'
    FROM locks as l
    LEFT JOIN pg_stat_activity as p on p.pid = l.pid
    WHERE l.pid is NULL
ON CONFLICT DO NOTHING;
...

```
It’s now possible to monitor jobs just by looking at the locks and released tables (filtering for only those rows where problem is not null if desired).

One final note about this approach: it doesn’t allow for “waiting” on a lock without further work. It’s not hard to add that capability on the python side if desired (catch database exceptions thrown from acquire and retry after sleeping).

