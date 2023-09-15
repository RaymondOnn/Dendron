---
id: 1mxfq6cn9pd4xf8ffkeyqk5
title: Flask
desc: ''
updated: 1692366369989
created: 1692165593799
---

- Theory: https://www.youtube.com/watch?v=qbLc5a9jdXo&ab_channel=CalebCurry
- Adding Security: https://www.youtube.com/watch?v=3drtMrcfssk&ab_channel=SoumilShah
- https://www.freecodecamp.org/news/structuring-a-flask-restplus-web-service-for-production-builds-c2ec676de563/
- https://towardsdatascience.com/flask-in-production-minimal-web-apis-2e0859736df
- *** https://blog.miguelgrinberg.com/post/designing-a-restful-api-using-flask-restful

## Installation
```py
pip install flask flask-sqlachemy
```

## Hello World

```py
from flask import Flask 
from flask_restful import Api, Resource

app = Flask(__name__)
api = Api(app)

class HelloWorld(Resource):
    # for GET Request
    def get(self):
        # make sure the output is serializ-able
        return {"data": "Hello World"}
    
    # for POST request
    def get(self):
        # make sure the output is serializ-able
        return {"data": "Posted"}

# register resource. assign endpoint to /helloworld
api.add_resource(HelloWorld, "/helloworld")

if __name__ == "__main__":
    # run in debug mode
    app.run(debug-True)
```

## Passing Arguments


```py
class HelloWorld(Resource):
    # for GET Request
    def get(self, name, age):
        # make sure the output is serializ-able
        return {"data": name, "age": age}
    
# parameters are passed by joining to the end of the endpoint url
# define parameter 'name' as string type
# define parameter 'age' as int type
api.add_resource(HelloWorld, "/helloworld/<string:name>/<int:age")

```

## Storing data to memory

```py
names = {
    "tim": {"age": 19, "gender": "male"},
    "bill": {"age": 70, "gender": "male"}
}

class HelloWorld(Resource):
    # for GET Request
    def get(self, name, age):
        # make sure the output is serializ-able
        return names[name]
    
# parameters are passed by joining to the end of the endpoint url
# define parameter 'name' as string type
# define parameter 'age' as int type
api.add_resource(HelloWorld, "/helloworld/<string:name>/<int:age>")

if __name__ == "__main__":
    # run in debug mode
    app.run(debug-True)
```

##### Viewing PUT request payload

```py
from flask_restful import reqparse

...

video_put_args = reqparse.RequestParser()
# define schema of PUT payload 
# Used in validating PUT payloads
video_put_args.add_argument("name", type=str, help="Name of the video is required". required-True)
video_put_args.add_argument("views", type=int, help="Views of the video is required", required-True)
video_put_args.add_argument("likes", type=int, help="Likes of the video is required", required-True)

videos = {}

class Video(Resource):
    # for GET Request
    def get(self, video_id):
        return videos[video_id]

    def put(self, video_id):
        args = video_put_Args.parse_args()
        return {video_id: args}
    
# parameters are passed by joining to the end of the endpoint url
api.add_resource(HelloWorld, "/helloworld/<int:video_id>")

if __name__ == "__main__":
    # run in debug mode
    app.run(debug-True)
```

##### Sending status code

```py
...

class Video(Resource):
    ...

    def put(self, video_id):
        args = video_put_Args.parse_args()
        video[video_id] = args
        # return status code 201
        return video_id[video_id], 201
    
```

##### Validating requests

```py
from flask_restful import abort

...

videos = {}

def abort_if_video_id_doesnt_exist(video_id):
    if video_id not in videos:
        abort(404, message="Could not find video...")

class Video(Resource):
    # for GET Request
    def get(self, video_id):
        # abort if GET request validation fails
        abort_if_video_id_doesnt_exist(video_id)
        return videos[video_id]

    ...
```

##### Handling DELETE request

```py
from flask_restful import reqparse

...

videos = {}
def abort_if_video_id_doesnt_exist(video_id):
    if video_id not in videos:
        abort(404, message="Could not find video...")

def abort_if_video_exist(video_id):
    if video_id in videos:
        abort(409, message="Video already exists with that ID...")

class Video(Resource):
    def get(...):
        ...

    def put(self, video_id):
        abort_if_video_exist(video_id)
        args = video_put_Args.parse_args()
        video[video_id] = args
        # return status code 201
        return video_id[video_id], 201

    def delete(self, video_id):
        abort_if_video_id_doesnt_exist(video_id)
        del videos[video_id]
        return '', 204
    

```

##### Adding a database

```py
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
api = Api(app)

# change config settings
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///database.db'
db = SQLAlchemy(app)

# define schema
class VideoModel(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    views = db.Column(db.Integer, nullable=False)
    likes = db.Column(db.Integer, nullable=False)

    def __repr__(self):
        return f"Video(name={name}, views={views}, likes={likes})"

# create the database
db.create_all()      

...

class Video(Resource):
    ...
    

```

##### Querying database

```py
from flask_sqlalchemy import SQLAlchemy
from flask_restful import fields, marshal_with 

...

# define db schema
class VideoModel(db.Model):
    ...

...

# define how data is to be serialized before sending out
resource_fields = {
    'id': fields.Integer,
    'name':fields.String,
    'views': fields.Intwger,
    'likes': fields.Integer
}

class Video(Resource):
    # decorator implies that the output will be serialized into json as defined in resource_fields
    @marshal_with(resource_fields)     
    def get(self, video_id):
        # query database
        result = VideoModel.query.filter_by(id=video_id).first()
        if not result:
            abort(404,message="Could not find video with that id")
        return result

    def put(self, video_id):
        ...

    def delete(self, video_id):
        ...
    

```

##### Inserting into database

```py
from flask_sqlalchemy import SQLAlchemy
from flask_restful import fields, marshal_with 

# define db schema
class VideoModel(db.Model):
    ...
    
...

# define how data is to be serialized before sending out
resource_fields = {
    'id': fields.Integer,
    'name':fields.String,
    'views': fields.Intwger,
    'likes': fields.Integer
}

class Video(Resource):
    # decorator implies that the output will be serialized into json as defined in resource_fields
    @marshal_with(resource_fields)     
    def get(self, video_id):
        ...

    @marshal_with(resource_fields)
    def put(self, video_id):
        # get fields of PUT payload as defined
        args = video_put_args,parse_args()
        result = VideoModel.query.filter_by(id=video_id).first()
        if result:
            abort(409, message-"Video id taken...")
            
        video = VideoModel(
            id=video_id, 
            name=args['name'],
            views=args['views'],
            likes=args['likes']
        )
        db.session.add(video)    # insert record
        db.session.commit()      # save operation
        # return status code 201
        return video, 201

    def delete(self, video_id):
        ...
    

```

##### Updating database records

```py
from flask_sqlalchemy import SQLAlchemy
from flask_restful import fields, marshal_with 

# define db schema
class VideoModel(db.Model):
    ...
    
...

video_update_args = reqparse.RequestParser()
video_update_args.add_argument("name", type=str, help="Name of the video is required")
video_update_args.add_argument("views", type=int, help="Views of the video is required")
video_update_args.add_argument("likes", type=int, help="Likes of the video is required")

# define how data is to be serialized before sending out
resource_fields = {
    'id': fields.Integer,
    'name':fields.String,
    'views': fields.Intwger,
    'likes': fields.Integer
}

class Video(Resource):
    # decorator implies that the output will be serialized into json as defined in resource_fields
    @marshal_with(resource_fields)     
    def get(self, video_id):
        ...

    @marshal_with(resource_fields)
    def put(self, video_id):
        ...

    @marshal_with(resource_fields)
    def patch(self, video_id):
        args = video_update_args.parse_args()  # value defaults to None    
        result = VideoModel.query.filter_by(id=video_id).first()
        if not result:
            abort(404, message="Video doesn't exist. Cannot Update..")
        
        if args['name']:
            result.name = args['name']
        if args['views']:
            result.views = args['views']
        if args['likes']:
            result.likes = args['likes']
        
        db.session.commit()
        return result, 201


    def delete(self, video_id):
        ...
    

```

### Full Example

```py
from flask import Flask 
from flask_restful import Api, Resource, reqparse, fields, marshal_with 
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
api = Api(app)

# change config settings
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///database.db'
#app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)

# define db schema
class VideoModel(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    views = db.Column(db.Integer, nullable=False)
    likes = db.Column(db.Integer, nullable=False)

    def __repr__(self):
        return f"Video(name={name}, views={views}, likes={likes})"

# create the database
db.create_all()  
    
...

video_put_args = reqparse.RequestParser()
# define schema of PUT payload 
# Used in validating PUT payloads
video_put_args.add_argument("name", type=str, help="Name of the video is required". required-True)
video_put_args.add_argument("views", type=int, help="Views of the video is required", required-True)
video_put_args.add_argument("likes", type=int, help="Likes of the video is required", required-True)


video_update_args = reqparse.RequestParser()
video_update_args.add_argument("name", type=str, help="Name of the video is required")
video_update_args.add_argument("views", type=int, help="Views of the video is required")
video_update_args.add_argument("likes", type=int, help="Likes of the video is required")

# define how data is to be serialized before sending out
resource_fields = {
    'id': fields.Integer,
    'name':fields.String,
    'views': fields.Intwger,
    'likes': fields.Integer
}

class Video(Resource):
    # decorator implies that the output will be serialized into json as defined in resource_fields
    @marshal_with(resource_fields)     
    def get(self, video_id):
        # query database
        result = VideoModel.query.filter_by(id=video_id).first()
        if not result:
            abort(404,message="Could not find video with that id")
        return result

    @marshal_with(resource_fields) 
    def put(self, video_id):
        # get fields of PUT payload as defined
        args = video_put_args,parse_args()
        result = VideoModel.query.filter_by(id=video_id).first()
        if result:
            abort(409, message-"Video id taken...")
            
        video = VideoModel(
            id=video_id, 
            name=args['name'],
            views=args['views'],
            likes=args['likes']
        )
        db.session.add(video)    # insert record
        db.session.commit()      # save operation
        # return status code 201
        return video, 201

    @marshal_with(resource_fields)
    def patch(self, video_id):
        args = video_update_args.parse_args()  # value defaults to None    
        result = VideoModel.query.filter_by(id=video_id).first()
        if not result:
            abort(404, message="Video doesn't exist. Cannot Update..")
        
        if args['name']:
            result.name = args['name']
        if args['views']:
            result.views = args['views']
        if args['likes']:
            result.likes = args['likes']
        
        db.session.commit()
        return result, 201


    def delete(self, video_id):
        ...
        return '', 204
    
# parameters are passed by joining to the end of the endpoint url
api.add_resource(HelloWorld, "/video/<int:video_id>")

if __name__ == "__main__":
    # run in debug mode
    app.run(debug-True)

```