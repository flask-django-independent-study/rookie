# SQL & SQLAlchemy

#### SQL databases are important to understand how to use, but writing a CRUD application using SQL statements is tedious.

SQLAlchemy is an ORM - an Object Relational Mapper. This means that it takes an object-oriented programming language -- like Python -- and maps objects that we understand to the table structure of an SQL database. This makes it much more intuitive, and this abstraction enables us to much more easily build data driven applications.

In order to use SQL without an ORM, we would need to learn how to write SQL statements. This is analagous to learning a new programming language: SQL is a bit strange, and there's a lot of new syntactical things to learn. While it may be useful how to write raw SQL statements in the future, it's really not that useful to us right now. That's why this week we're going to focus on learning the basics of SQL using SQLAlchemy as our ORM.

#### SQLite vs. MySQL or PostgreSQL

For this tutorial, we will be using SQLite. This is a great way to get started with testing CRUD routes and not dealing with the hassle of a production database. We'll get more into deployment and production databases a bit later.

The primary difference between SQLite and MySQL or PostgreSQL is that our database is actually not going to be hosted on its own server. Instead, it's going to reside in our file system. This is not scalable for larger projects with lots of data (or very secure), but it's easy to set up and avoids the learning curve that comes with installation and proper setup of MySQL or PostgresQL. At the end of the term, I'll create a tutorial for switching over to a production database and deploying your application. For now, let's just focus on understanding SQL & our ORM in practice.

If you're interested in understanding more about *which* production SQL database manager you should choose when you're working on production applications, [this](https://www.postgresqltutorial.com/postgresql-vs-mysql/) article is an extremely helpful resource to compare the two.

#### Let's get started!

1. Clone into the repository:

```zsh
git clone https://github.com/flask-django-independent-study/rookie-events-week-4
```

You're going to notice the file structure is a bit different this week - and that there may be some new things that you don't totally understand (like the routes being @main.route, for example).

This file structure is due to the fact that to avoid circular import issues, I split the starting code up into *packages* - and I threw in a *blueprint* so that Flask knew where to find our routes. I'll mention this a few times throughout this assignment, but we'll be learning more about blueprints and packages in a couple of weeks. For now, don't worry too much about this new structure.

If you **DO** want to learn more about what I'm doing and you just can't wait, check out the [Varsity week one assignment](https://github.com/flask-django-independent-study/varsity/blob/master/Assignments/Week-1.md)!

2. Set up your virtual environment! You all should be good on this, but if you have questions, check online for resources OR check out the [Rookie Week One assignment](https://github.com/flask-django-independent-study/rookie/blob/master/Assignments/Week-1-Halloween-Party.md) for a refresher!

3. Install the requirements. This week, that's going to include Flask, SQLAlchemy, and our existing requirements from last week.

```zsh
pip3 install -r requirements.txt
```

4. In your directory, check out the example database model in events_app/example.py.

*What are some things that you notice about this model?*

It's similar to a class, but it's a bit special, because it inherits from a class that we have access to through SQLAlchemy. You'll notice that we don't need to define an `__init__()`, and that instead of initializing *properties* we're initializing *attributes*. There are some other special things about this class, too.

Notice that each attribute we initialize instantiates an object of the Column class that we can access through our database object. What these attributes are defining are *columns* of our SQL database *tables*. This is a lot of new terminology, and I'll be linking several [resources](https://github.com/flask-django-independent-study/rookie/blob/master/Resources/Week-4.md) in case you can't keep things straight. The good news is that with SQLAlchemy, a lot of this is abstracted away and you don't really need to worry about the specifics when we're coding, it's just good for you to understand what's going on behind the scenes.

When we instantiate the Column object, it looks like we also provide some parameters. For example, our id Column has a parameter called "primary_key" that's set to True. This means that this is the *primary* unique identifier for this object. In order to really explain what this means, I'm going to go on a slight side tangent:

One of the key advantages to using a SQL database versus a NoSQL database is the ability to create *relationships* between tables. To illustrate:

> In this application you will have two tables that you'll be working with: a Guest table and an Event table. There is a relationship between the two, right? Guests RSVP to Events, and Events likely want to track how many and which guests will be attending. This would be useful in a production application for several reasons: the hosts could contact guests, add them to email lists, and know what kinds of events they prefer, for example. There are many more uses for this, but for now let's focus on our use case. There are a few key kinds of relationships that you can create between records of any database. I'll link a resource describing these in more detail later, but conceptually they're simple: you have One-to-Many relationships, One-to-One relationships, and Many-to-Many relationships. Can you guess which kind of relationship our Guests and Events will have?

It looks like we'll be having a Many-to-Many relationship between our guests and events, right? Many guests will attend an event, and a single guest can RSVP to many events! So, now to get back to primary keys, we're going to need a way to *reference* events that guests are attending and guests that are attending events. This is what primary keys do for us. Aside from ensuring that even if we have multiple guests named John Smith we have a way to query for the *correct* John Smith, primary keys enable us to link tables together through back referencing ids that belong to related tables.

If this all sounds like a lot, don't worry. You'll understand in more detail once we're done coding. If you ever get stuck, don't forget: we're linking lots of [resources](https://github.com/flask-django-independent-study/rookie/blob/master/Resources/Week-4.md) for you and there's no shame in Googling anything additional or asking questions.

The other parameters that we have to provide are our *data types*. Most of these should look familiar: integers, strings, and booleans. These are also objects that SQLAlchemy provides in order to abstract away the nitty-gritty SQL data types that can be confusing at first, and calls them things we understand as Pythonic.

We're going to be using models like this to represent tables in our database. But first, we have some setting up to do.

6. I've included a file called config.py in your project. This accesses important environment variables from our .env file that we created last week, and stores them in another special class called Config. I'll talk more about configuration later on in the course when we go over using blueprints & packages.

7. You're going to need to add something to your .env file, since I'm following best practices, and not pushing mine to Github. While our SQLite URI isn't secret, we're going to start using best practices for setup right off the bat. Production database URIs and URLs will contain information you *don't* want shared. For this reason, we're going to hide our database URI environment variable and then access it from our Config file.

```
SQLALCHEMY_DATABASE_URI=sqlite:///database.db
```

This statement sets the database URI (the address of the database we're using) to a sqlite (testing) database, and we told it to create it at "database.db". This is an important environment variable that will tell SQLAlchemy where to insert and query for things. For now, database.db is going to be our database.

I've included the statement in our config.py file that we need to access this variable. You can check it out to see how it's done. We access SQLALCHEMY_DATABASE_URI from our .env file the same way we did with out API_KEY, but now these have both been moved into our config.py file rather than remaining at the top of our routes.

8. You'll notice now at the top of `__init__.py` there is a TODO. We need to add this line:

```python
app.config.from_object(Config)
```

What we've done here is moved our API_KEY variable into our Config file, and now we're instantiating the entire configuration object with all of our variables using the statement above. I will also discuss this statement and what we're doing in more detail later on when we look at blueprints and packages.

8. We need to add an additional import statement to the top of our app.py file so that we can intialize our database. It should look like this:

```python
from flask_sqlalchemy import SQLAlchemy
```

8. In your app.py file, make sure you follow the TODOs to initialize your database. This line should look like:

```python
db = SQLAlchemy(app)
```

9. We're going to try out creating our database now! Below, I'm going to teach you a way of creating it that is less than ideal. Then, I'm going to teach you an easier way in a moment. I feel like it's important for you to understand first what's happening behind the scenes, and then abstracting things from there.


10. Once you've gotten that done, go to your terminal and make sure you're in the project directory. In your terminal, run:

```zsh
python3
```

This should begin running python. In your terminal you should see the three little arrows like this:

```zsh
>>>
```

Indicating that python is able to take commands.
Run the following:

```zsh
from events_app import db
```

The reason that we run the above line is that we initialize our SQLAlchemy object (which we refer to as our database or db) in our `__init__.py` file in our events_app folder, or package. This will make more sense when we talk about blueprints and packages!

Then:

```zsh
db.create_all()
```

You should see a file called database.db appear on the left hand side of your screen. Great job!

*If you get an error in your terminal that says "AttributeError: 'NoneType' object has no attribute 'drivername'", use command + d or exit() to exit the python editor and run the following:*

```zsh
export SQLALCHEMY_DATABASE_URI=sqlite:///database.db
```

*The reason you're getting this error is because we set the database URI and then never ran our app.py file: meaning the code we wrote to access this URI and pass it to our database was never executed. You'll encounter this error from time to time if you change your URI, move it, or update your Config file and then try to create your database without running your app first. It can be a tricky error to debug, which is why I want to make sure you know what it means and what's going on.*

11. Now, here's the annoying thing about SQL: every time you change the database models, you're going to need to re-create the database. To do this, do the following:

```zsh
python3
```

This should begin running python. In your terminal you should see the three little arrows like this:

```zsh
>>>
```

Indicating that python is able to take commands.
Run the following:

```zsh
from events_app import db
```

Then drop the current database:

```zsh
db.drop_all()
```

And create the new database:

```zsh
db.create_all()
```

Alternatively, you can delete the database.db file from your file tree, and follow the steps to just re-create it. Whichever works for you is fine.

14. Now, here's an easier way. What this will do is recreates our database if it doesn't exist. This enables us to not have to go through and recreate our database in the terminal each time we make a change to our database models. We just need to delete our database.db file when we make changes, and this line will re-create the database for us without us having to touch our terminal.

To implement this, go to the bottom of app.py and make sure that the statement that runs your app looks like this:

```python
if __name__ == "__main__":
    with app.app_context():
        db.create_all()
    app.run(debug=True)
```

Alternatively, you can add the following lines to the very bottom of your `__init__.py` file:

```python
with app.app_context():
  db.create_all()
```

Both work exactly the same way, and it really doesn't make a difference which one you run.

**Don't forget: every time we change our database models we will STILL need to delete our database.db file before re-running our app. If you start running into issues where you don't think your insert or delete statements are working, and you know you've made some changes to your models, delete your database.db file and let your app recreate it for you before further debugging.**

15. There are a few different things we're going to do with our new found database powers. I'm going to list them below so that you know where we're going with this application. I'm going to also include a demo video in your [resources](https://github.com/flask-django-independent-study/rookie/blob/master/Resources/Week-4.md) so that if you get stuck or aren't clear on our end goal, you can reference this.

    1. We want to make sure that our guests are stored in our database, and not just in memory (in a variable, as they are now).
    2. I want us to be able to create events, and store those in a database as well. We'll be displaying our events on our homepage for users to see.
    3. After that, we're going to have an RSVP form for each event in our database, and keep track of which guests are going to which event. This means that we're going to have to dive into relationships, which are arguably the most important part of SQL databases.
    4. I want users to be able to click an event on the homepage and access the RSVP form for it. There are lots of stretch challenges that could be involved in this (like creating a **modal** to display the form on the homepage) but for now, we'll just make events link to the RSVP page.
    5. I want us to be able to edit and delete events that we've created. When we get into user authentication and roles, we'll make sure only an admin user can add, edit, and delete events. For now, though, everyone will have access to these features.

    So, there are a few things we're going to need. We're going to need a Guests and an Events table in our database, so we'll need to write the corresponding models for these. We'll need to add some insert statements to our routes, and we're going to need to add some new routes to handle adding, editing and deleting events.

    That's a lot of work to do, so let's get started!

16. There are a lot of TODOs in our main/routes.py file. Let's get started. First, though, I think we should create a file called models.py within our event_app folder.

In models.py, I want you to write two models using the example in events_app/example.py. I'll give you the code snippets you'll need below if you need help, but try this on your own before comparing to mine so that you can practice thinking through these things and looking things up.

Our Guest model is going to inherit from db.Model. This means that you'll need to add an import statement at the top of your file so that we can access the database object that we created as db. *Don't forget your docstrings at the top of every file*!

```python
from from events_app import db
# We're also going to need one more for our joining table:
from sqlalchemy.orm import backref
```
Our Guest model needs the following attributes:

  1. an id: which will be an Integer type, and will be the primary key.
  2. name: which will be a string. Make sure you pass a maximum number of characters, say, 55?
  3. email: will also be a string. Same thing, probably 40 characters is enough, but you can deviate from this.
  4. plus_one: will be a string (likely a name). Make sure you also put a cap on this, maybe the same as the name field. You can also set nullable=True on this one: it's not required that a guest brings a plus one. We'll want to be able to set this to None if they don't have one.
  5. phone: this one is a bit weird. We don't really have a datatype for a phone number. But, since we aren't doing much with this data yet, let's just make it a string for now. It doesn't need to be more than 10-15 characters I believe (depending on the country you're in)
  6. events attending will look like this: events_attending = db.relationship("Event", secondary="guest_event_link")

  Events attending looks weird, right? This is because it defines a *relationship*. The biggest benefit to SQL databases versus No SQL databases is their ability to maintain *relationships* between different tables. Large, data-driven applications often use SQL databases because of this. No SQL databases like Mongo can have collections that reference each other: but it gets a bit tricky to keep track of things because No SQL databases are much less structured.

  Because an event has many guests, and guests can RSVP to many events, we're going to have what is called a many-to-many relationship between the two (see further explanation at the top if you don't remember what this means). This means we're going to have to also have a third table that we won't do much with but that SQL needs to track our data, called a **joining table**. I'll help you write this because writing joining tables is tricky, but pay attention to the fields I add, and feel free to ask questions if I leave out any important information about what this is doing behind the scenes. I'll make sure to link lots of [resources](https://github.com/flask-django-independent-study/rookie/blob/master/Resources/Week-4.md) this week as well so that you can read more!

Now, let's work on writing out Event model. None of the following attributes will be nullable.

  1. id: This will be an Integer, and the primary key
  2. title: this will be a String, maybe with up to 90 characters?
  3. description: this will also be a String, let's keep it Tweet length: 140 characters
  4. date: this is going to be a DateTime object.
  5. time: This is also going to be a DateTime object.
  6. guests = db.relationship("Guest", secondary="guest_event_link")


Great! Now you've got two database models. But we aren't done yet!

17. Let's work on putting together our **joining table**. It's going to look like this:

```python
class GuestEventLink(db.Model):
    """Joining table for guests & events."""

    id = db.Column(db.Integer, primary_key=True)
    guest_id = db.Column(db.Integer, db.ForeignKey("guest.id"))
    event_id = db.Column(db.Integer, db.ForeignKey("event.id"))
    event = db.relationship(
        "Event", backref=backref("link", cascade="all, delete-orphan")
    )
    guest = db.relationship(
        "Guest", backref=backref("link", cascade="all, delete-orphan")
    )
```

You'll notice a few things about this table. First, you'll never instantiate this model as an object. This is a table that SQLAlchemy is going to handle for us behind the scenes.

Second, it looks like each entry in this table has its own id, too. This is important. Every entry, or *row* in a SQL database is going to have a unique ID that SQL sets for us.

Third, we're accessing our guest id and our event id as our *ForeignKey*. This term is referring to the fact that it's not the primary key for this record, but that it's accessing another table's ids to back reference its specific associated records!

Lastly, we also have a db.relationship field for each table we're connected with. This is referencing the specific tables, and the "cascade" parameter is telling it that if we delete an event, for example, we also need to delete all references to the event from the guests that were attending. This avoids weird errors later if we try to access events from guests and those events no longer exist.

18. *Spoiler* Below is the code from my models file, in case you run into any issues or just want to compare what you came up with to the way I wrote these. Remember: before we do anything else, like every time you touch this file, you're going to need to delete your database.db file and run your app again.

I've included a `__repr__` method in mine. This defines what is returned if we print out objects based on these classes. This is helpful during debugging. If you don't have this, printing these objects will return a strange object that looks something like [<This>] and doesn't show much useful information.

```python
"""Create database models to represent tables."""
from events_app import db
from sqlalchemy.orm import backref


class Guest(db.Model):
    """Create Guest model."""

    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(55), nullable=False)
    email = db.Column(db.String(40), nullable=False)
    plus_one = db.Column(db.String(55), nullable=True)
    phone = db.Column(db.String(12), nullable=False)
    events_attending = db.relationship("Event", secondary="guest_event_link")

    def __repr__(self):
        """Define how we want this to look when printed."""
        return self.name


class Event(db.Model):
    """Create Event model."""

    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(90), nullable=False)
    description = db.Column(db.String(140), nullable=False)
    date = db.Column(db.DateTime, nullable=False)
    time = db.Column(db.DateTime, nullable=False)
    guests = db.relationship("Guest", secondary="guest_event_link")

    def __repr__(self):
        """Define how we want this to look when printed."""
        return self.title, self.description


class GuestEventLink(db.Model):
    """Joining table for guests & events."""

    id = db.Column(db.Integer, primary_key=True)
    guest_id = db.Column(db.Integer, db.ForeignKey("guest.id"))
    event_id = db.Column(db.Integer, db.ForeignKey("event.id"))
    event = db.relationship(
        "Event", backref=backref("link", cascade="all, delete-orphan")
    )
    guest = db.relationship(
        "Guest", backref=backref("link", cascade="all, delete-orphan")
    )
```

19. In our routes now, we need to go through and finish our TODOs. I want to give you a few tips before we get started.

To run a query in SQLAlchemy, we're going to write a line like this:

```python
# To access all items from a table:

event = Event.query.all()

# To filter by attribute (this can be ANY attribute, it doesn't have to be id)

event = Event.query.filter_by(id=event_id).first()

# Adding .first() just grabs the first entry that matches. This is optional, but I find it's clean and avoids
# strange behavior if something went wrong adding items.
```

To update records, SQLAlchemy has abstracted things such that we're really just updating an object. So, if we want to update the title of an event, for example:

```python
event.title = new_title

db.session.commit()
```

You'll notice I threw something new in there: `db.session.commit()`. SQLAlchemy recognizes sessions (behind the scenes, SQL does too) and the commit() method is the abstracted way of committing out changes to SQL without having to write raw SQL statements. Unlike NoSQL databases, SQL databases require such a commit statement every time a change is made. Similarly, when you're adding an item to your database, you'll need to run both .add(object) and .commit. For adding a new Guest, it will look like this:

```python
name = request.form.get('name')

guest = Guest(name=name)

db.session.add(guest)
db.session.commit()
```

While there's a LOT of new things to learn here, it's all really pretty simple once you get into it.

Lastly, if we want to delete something, say, an event:

```python
event = Event.query.filter_by(id=event_id).first()
db.session.delete(event)
db.session.commit()
```

Just that simple! We love ORMs.

I'm going to let you go and work on completing the TODOs in the starting code now. If you have questions or get stuck, check out your [resources](https://github.com/flask-django-independent-study/rookie/blob/master/Resources/Week-4.md). I've included a demo video that should help you understand where we're going with this. I've also done most of the templating and styling for you to save you some time. Feel free to make these unique and make improvements where you see opportunities to!

### Wrap Up:

You've just written your first CRUD application. CRUD is an abbreviation that you'll be seeing often, and it refers being able to Create, Read, Update, and Delete information in your database. As I'm sure you can guess, these are all database operations that any application leveraging a database will likely need to use a *lot*. Now you know how to write all of these applications for a SQL database using an ORM!

### Stretch Challenges:

1. Our delete route kind of requires us to click a delete button. I'm lazy, and I don't like this. I feel like when the current date is past the date that the event occurred, this event should be automatically deleted. Add an if statement to your delete route to make this happen!

2. As always, update the styling and make it fun!

### Submission:

Link this in your tracker if you completed it so that you can get it reviewed! And, as always, if you find any typos, open an issue on the repository (either this one or the project repo). If you need clarification or have questions, come to office hours or send us a slack message!
