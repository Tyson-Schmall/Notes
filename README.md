This README file displays all of the information this repository contains. This is a basic, but detailed
walkthrough of how I structured out details on building an API our group used for a class project. Another
student had also put together something similar and we were able to communicate and cooperate well with each
other in order to get our project built out.

That student's name is Edwin Tinoco, his github URL is https://github.com/EdwinTinoco.
While I put this below code and instruction together all on my own, he had put one together on his own, too.
These were very similar and our project went over pretty well!

It also has user stories for mock examples and a prime objective. See everything below this point, it wasn't ENTIRELY
completed, but this was the bulk of it, for sure.

Activity checklist - App in a day Project

Open it. You have activities listed to choose from, once you select an activity, you will be redirected to one of two
screens. The screens should then show you an option to view hotspots within a given area of the zip code you enter.

The other option should be suggested gear to or extra items to bring and reasons for bringing them. Information like cost,
use cases, necessity vs for fun, etc.

The option at the bottom of both pages should be to return to the home page to select another activity.

Prime Objective - Our prime objective is to help people find an outdoor activity to do, and to aid by preparing them with access
to knowledge and gear/accessories (if recommended or required).

User Story Tyson - Tyson likes to go outside. He isn't able to right now because of quarantine. When quarantine is done,
he is going to make every effort to go outside that he possibly can. He is actively searching for new activities to try,
as well as accessories and required gear he would need to do these new activities. He is kind of illiterate when it
comes to apps and mobile devices in general, so he needs something user friendly.

User Story Joe - Joe likes to do all kinds of things outdoors. Fishing is his main hobby. Coincidentally, he only knows of a
couple fishing spots in his local area. He is just looking for something new, maybe for other outdoor activities as well.

User Story Edwin - Edwin is the man. Edwin likes to do EVERYTHING outdoors, so he is going to be looking through this app from
top to bottom. He is going to use this app every day the weather is nice.

User Story Traveler - This app is the Traveler's best friend. Travelers usually venture to new areas with specific plans and ideas
in mind about what they want to do. They want to see the scenery and experience the atmosphere of these new and
unfamiliar places. Finding local hotspots for activities is easy and convenient for this user.

User Story Seldom-Adventurer - The Seldom-Adventurer is one who is not all that particular about what they do outdoors. They may
use our app when the weather is nice and they don't have much to do BECAUSE of the quarantine. This user logs on just to kind of
scour information pertaining to the most common/popular activities/activity spots for each different activity type.

Getting the virtual environment going to build the API:

We must make sure we are using Python and pipenv commands within the repository we clone.
First and foremost, clone the repository from github, and checkout a branch from it. We want to make sure that we then CD into
the folder that we have checked out.

- cmd prompt
- CD to folder I want to have project clone live in
- git clone {([ PASTE THE REPOSITORY URL ])}
- git checkout -b "name-of-branch-goes-here"
- CD into downloaded branch folder/directory.

From there, we create the pip file and are able to start adding our own dependencies. We do need to add
Flask to this file. So the next thing we do is type in pipenv install flask.

- pipenv --three
- pipenv install flask

Once this is done, we should create a new file called app.py. Then, we import flask to app.py.

- from flask import Flask, request, jsonify (on app.py file)

From here, we can create a variable, named whatever we want, but likely we will call it 'app' and set it equal to Flask(**name**)
The reason we instantiate Flask into a variable like this, is so we can call this instance later on and run it.

- app = Flask(**name**)

Then we can create routes. Routes lead to end points. To setup a route, we must use a decorator ( @ ).

So we tie this @ to the name of the variable. So @app.route(" "). The quotes after route, they take in whatever the endpoint will
be. So if our front end guys are working and their endpoint is share-activity.html, then our @app.route("/share-activity") is how it would look.
\*\*Double check that with Kent and Ryan, however.

We can then install SQLAlchemy. SQLAlchemy is an ORM, which stands for Object Relational Mapper. ORM gives you the ability to
communicate with a databse without having to write pure SQL code.

Then Flask Marshmallow. Render JSON API's and to have helpful tools to wrap the code. To render JSON, can be confusing, but marshmallow
allows us to call a coupe of libraries and it makes rendering a little bit easier. We can build our own models and then create a schema
to map out how our data will look, and what our code will look like.

The last one we install is called marshmallow-sqlAlchemy. This allows us to connect the first two mentioned above, and gives us access
to other powerful tools we may need to build out certain things wihtin our database.

- pipenv install Flask-SQLAlchemy **_May take some time_**
- pipenv install flask-marshmallow **_May take some time_**
- pipenv install marshmallow-sqlalchemy **_May take some time_**

This should now have Flask and the above three dependencies installed into the branch we have created locally. We should be able to do
this.

Go to app.py.

=> (from flask import Flask <= already exists at top of app.py file) <=

- from flask_sqlalchemy import SQLAlchemy
- from mashmallow import Mashmallow
- import os

Now that all this is done, we can build out database tables using schema. This is where we have to create our own stuff.
We would do well to start by creating a variable calld basedir and set it equal to our base directory. This is where the
SQLTable is going to be saved. The syntax is a little weird on this.

- basedir = os.path.abspath(os.path.dirname(**file**))
- app.config["SQLALCHEMY_DATABASE_URI"] = "sqllit:///" + os.path.join(basedir, "app.sqlite")
- db = SQLAlchemy(app)
- ma = Marshmallow(app)

With the above classes being instantiated the way they are, we can now build our schema and structure our database tables.

- class Activity(db.Model):
  id = db.Column(db.Integer, primary_key=True)
  heading = db.Column(db.String(50), unique=False)
  text = db.Column(db.String(96), unique=False)

  def **init**(self, heading, text):
  self.heading = heading
  self.text = text

- class ActivitySchema(ma.Schema):
  class Meta:
  fields = ("heading", "text")

- activity_schema = ActivitySchema()
- activities_schema = ActivitySchema(many=True)

* if **name** == "**main**":
  app.run(debug=True)

This is how we build out our entire database table. This is a singular database table build.
It now is ready for us to build out an entire API.

Time to build out the endpoint for each activity. Start with a decorator, then call on the variable we instantiated Flask into, so we can
utilize Flask to build our route.

- @app.route("/share-activity", methods=["POST"])
  def add_activity():
  heading = request.json['heading']
  text = request.json['text']

      new_activity = Activity(heading, text)

      db.session.add(new_activity)
      db.session.commit()

      activity = Activity.query.get(new_activity.id)

      return activity_schema.jsonify(activity)

++ Check in with postman at this point, and make sure your server is running in your cmd. Check out localhost 5000/share-activity, change type to POST, then
++ click 'body', then 'raw', then change orange lettered word 'text' to JSON(application/json). From there, we should be able to add an object
++ with the keys "heading" and "text" and these would be what people share or 'post' to our database. Other experiences for people to checkout.
++ Maybe there is a way to implement pictures with this?

++ Below is how we can create the GET url, which will show us all suggested activities in the system.

- @app.route("/shared-activities", methods=["GET"])
- def get_activities():
  all_activities = Activity.query.all()
  result = activities_schema.dump(all_guides)
  return jsonify(result.data)

++ Check in with postman at this point, and make sure your server is running in your cmd. Check out localhost 5000/shared-activities, change type to GET. Then
++ click 'Send'.

+++ Below is how to query for a single activity submission.

- @app.route("/share-activity/<id>, methods=["GET"])
- def get_activity(id):
  guide = Activity.query.get(id)
  return activity_schema.jsonify(activity)

++ With the above code, you can now do a 'get' request on postman from our localhost:5000/share-activity, and add a '/insert-number-here'. This will return
++ the specific 'share-activity' that was 'post'-ed. This will not return an error if an invalid ID is passed in, you will just get an empty object.
++ Keep this in mind.

+++++++++++++ Now the feature has been built out for sharing other activities and for seeing the activies that have been shared by everybody else so far. Just
+++++++++++++ like Angela had suggested.

++ Below is how to modify a post that has already been created.

- @app.route("/activity/<id>", methods=["PUT"]
  def activity_update(id):
  activity = Activity.query.get(id)
  heading = request.json['heading']
  text = request.json['text']
  activity.heading = heading
  activity.text = text
  db.session.commit()
  return activity_schema.jsonify(guide)

+++ Now if I visit the postman localhost:5000/share-activity/1, as a PUT request, I can modify the '1' activity's information/data/values.
