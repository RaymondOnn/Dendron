---
id: i7map6r8sj3ri04b7jh00d5
title: database
desc: ''
updated: 1706176298866
created: 1705821371556
---
- flask-sqlachemy: [declarative_base vs db.model](https://stackoverflow.com/questions/22698478/what-is-the-difference-between-the-declarative-base-and-db-model)

## flask-sqlalchemy

### Creating Models
- https://www.geeksforgeeks.org/sqlalchemy-mapping-table-columns/

### One-to-many relationships

``` py
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy(app)

class Owner(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(20))
    address = db.Column(db.String(100))    

    # relationships
    # Each owner can have many pets
    pets = db.reltionship('Pet', backref='Owner')

class Pets(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(20))
    age = db.Column(db.Integer) 
    # Each pet has one owner
    owner_id = db.Column(db.Integer, db.ForeignKey('owner_id'))

# querying
anthony.pets
>>> [<Pet 1>]
anthony.pets[0].name
>> 'Max'    
```
### Many-to-many relationships
``` py
user_channel = db.Table(
    'user_channel', 
    db.Column('user_id', db.Integer, db.ForeignKey('user.id')),
    db.Column('channel_id', db.Integer, db.ForeignKey('channel.id')),
)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(20))

    # relationships
    # secondary: bridge table faciliating the relationship
    # backref: pseudo col that is created on channel table
    following = db.relationship('Channel', secondary=user_channel, backref='followers' )

    def __repr__(self):
        return f'<User: {self.name}>'

class Channel(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(20))

    def __repr__(self):
        return f'<Channel: {self.name}>'


# Querying
from app import db, User, Channel

erin = User.query.filter_by(name='Erin').first()
erin.following
>>> [<Channel: Pretty Printed>]

# 
erin.following.remove(mrbeast)
db.session.commit()
```

### [Querying: New vs Old style](https://www.youtube.com/watch?v=vMkHXB1aPL4)

``` py
# User is the data model

# Get All Records
User.query.all()  # old
db.session.execute(db.select(User)).scalars().all() # new, long version
db.session.scalars(db.select(User)).all() # new, short version

# Get 1st record of results
User.query.first()  # old
db.session.scalars(db.select(User)).first() # new, short version

# Get record that match value
User.query.filter_by(name="Pretty Printed").first # old
db.session.scalars(db.select(User).filter_by(name="Pretty Printed")).first() # new, short version

# Get records that exclude value
User.query.filter(User.name != "Anthony").all() # old
db.session.scalars(db.select(User).where(User.name != "Anthony")).all() # new, short version

# Get 4th record
User.query.get(4) # old
db.session.get(User, 4) # new, short version

# Get number of records
User.query.count()  # old
# Must count a field, counting rows doesn't work
db.session.scalar(db.func.count(User.id)) # new, short version



User.query.order_by(User.name.desc()).all()
```
#### Operators: `and_`(`&`), `or_`(`|`), NOT(`~`)


- Suppose this query:
``` sql
SELECT 
    address 
FROM addressbook 
WHERE 
    city='boston' 
    AND (
            lastname='bulger' 
        OR 
            firstname='whitey'
    )
```

### Decoupling sqlalchemy
- https://staskoltsov.medium.com/demystifying-flask-sqlalchemy-a3d8a786ed2f