---
id: ccda2zjusxvpg4f1ct2r3yk
title: Mongoengine
desc: ''
updated: 1691981586430
created: 1691981558588
---
- link: https://www.youtube.com/watch?v=E-1xI85Zog8&ab_channel=freeCodeCamp.org


## What is MongoDB?
- NoSQL Database (**N**ot **O**nly **SQL**)
- Data in stored as documents
  - Each row of data stored in a document as key value pairs similar to json format

```json
{
    "_id": 1001,
    "title": "Welcome to the course",
    "course_id": 1,
    "duration_in_sec": 1707,
    // Consider lectures to be a pre-computed join
    // Saves on time and compute to do joins
    "lectures": [
        {
            "id": 10101,
            "title": "Welcome and thanks for coming".
            "video_irl": "https://s3.amazonaws.com/.../00-00-welcome.mp4",
            "duration_in_sec": 380
        },
         //...
        {
            "id": 10106,
            "title": "Linux: Installing Python and PyCharm",
            "video_irl": "https://s3.amazonaws.com/.../00-05-linux-setup.mp4",
            "duration_in_sec": 387
        }, 
    ]
}
```

## Data Modeling in Document databases
- To embed or not to embed?
  1. Is the embedded data wanted most (80%) of the time? Embed if YES
  2. How often do you want the embedded data without the containing document? Not Embed if YES
  3. Is the embedded data a bounded set? Embed if YES
  4. Is that bound small? Embed if YES
  5. How varied are your queries? 
  6. Is this an integration DB or an application DB? 


## Python with MongoDB
- Github for example code: https://github.com/mikeckennedy/mongodb-quickstart-course/tree/master/src/snake_bnb/src/data
```py
pip install mongoengine
```

#### Connections with MongoDB

``` py
alias_core = 'core'
db = 'snake_db'

data = dict(
    username=...,
    password=...,
    host=...,
    ...
)
mongoengine.register_connection(alias=alias, name=db, **data)
```


``` py
# mongo_setup.py
import mongoengine

def global_init()
    mongoengine().register_connection(
        alias='core',
        name='snake_bnb'
    )

# main.py
import mongo_setup as mongo_setup

def main():
    mongo_setup.global_init()    

```

#### Defining Schema: Data Types, Constraints, Default and Required Values
- 
``` py
# cages.py
import datetime
import mongoengine

from data.bookings import Booking


class Cage(mongoengine.Document):
    registered_date = mongoengine.DateTimeField(default=datetime.datetime.now)

    name = mongoengine.StringField(required=True)
    price = mongoengine.FloatField(required=True)
    square_meters = mongoengine.FloatField(required=True)
    is_carpeted = mongoengine.BooleanField(required=True)
    has_toys = mongoengine.BooleanField(required=True)
    allow_dangerous_snakes = mongoengine.BooleanField(default=False)

    bookings = mongoengine.EmbeddedDocumentListField(Booking)

    meta = {
        'db_alias': 'core',
        'collection': 'cages'
    }

# bookings.py
import mongoengine


class Booking(mongoengine.EmbeddedDocument):  # NOTE: mongoengine.EmbeddedDocument!!!
    guest_owner_id = mongoengine.ObjectIdField()
    guest_snake_id = mongoengine.ObjectIdField()

    booked_date = mongoengine.DateTimeField()
    check_in_date = mongoengine.DateTimeField(required=True)
    check_out_date = mongoengine.DateTimeField(required=True)

    review = mongoengine.StringField()
    rating = mongoengine.IntField(default=0)

    @property
    def duration_in_days(self):
        dt = self.check_out_date - self.check_in_date
        return dt.days

```