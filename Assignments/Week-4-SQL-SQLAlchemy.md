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

2. Set up your virtual environment! You all should be good on this, but if you have questions, check online for resources OR check out the Rookie Week One assignment for refresher!

3. Install the requirements. This week, that's going to include Flask, SQLAlchemy, and our existing requirements from last week.

```zsh
pip3 install -r requirements.txt
```

4. In your directory, check out the example database model in example.py.

*What are some things that you notice about this model?*

It's similar to a class, but it's a bit special, because it inherits from a class that we have access to through SQLAlchemy. You'll notice that we don't need to define an `__init__()`, and that instead of initializing *properties* we're initializing *attributes*. There are some other special things about this class, too.

Notice that each attribute we initialize instantiates an object of the Column class that we can access through our database object. What these attributes are defining are *columns* of our SQL database *tables*. This is a lot of new terminology, and I'll be linking several resources in case you can't keep things straight. The good news is that with SQLAlchemy, a lot of this is abstracted away and you don't really need to worry about the specifics when we're coding, it's just good for you to understand what's going on behind the scenes.

When we instantiate the Column object, it looks like we also provide some parameters. For example, our id Column has a parameter called "primary_key" that's set to True. This means that this is the *primary* unique identifier for this object. I'll describe this in more detail later, and you'll also be able to read more about primary and foreign keys in your resources.

The other parameters that we have to provide are our *data types*. Most of these should look familiar: integers, strings, and booleans. These are also objects that SQLAlchemy provides in order to abstract away the nitty-gritty SQL data types that can be confusing at first, and calls them things we understand as Pythonic.

We're going to be using models like this to represent tables in our database. But first, we have some setting up to do.

6. I've included a file called config.py in your project. This accesses important environment variables from our .env file that we created last week, and stores them in another special class called Config. I'll talk more about configuration later on in the course when we go over using blueprints & packages.

7. You're going to need to add something to your .env file, since mine obviously doesn't get pushed to GitHub. While our SQLite URI isn't secret, we're going to start using best practices for setup right off the bat. For this reason, we're going to hide our database URI environment variable and then access it from our Config file.

```python
SQLALCHEMY_DATABASE_URI = "sqlite:///database.db"
```

This statement sets the database URI (the address of the database we're using) to a sqlite (testing) database, and we told it to create it at "database.db". This is an important environment variable that will tell SQLAlchemy where to insert and query for things. For now, database.db is going to be our database.

8. You'll notice now at the top of app.py there is a line that looks like this:

```python
app.config.from_object(Config)
```

What we've done here is moved our API_KEY variable into our Config file, and now we're instantiating the entire configuration object with all of our variables using the statement above. I will also discuss this statement and what we're doing in more detail later on when we look at blueprints and packages.

8. We need to add an additional import statement to the top of our app.py file so that we can intialize our database. It should look like this:

```python
from flask_sqlalchemy import SQLAlchemy
```

8. In your app.py file, make sure you follow the TODOs to initialize your database. This line should look something like:

```python
db = SQLAlchemy(app)
```

9. We're going to need to create our database now. Below, I'm going to teach you a way of creating it that is less than ideal. Then, I'm going to teach you an easier way in a moment. I feel like it's important for you to understand first what's happening behind the scenes, and then abstracting things from there.


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
from app import db
```

Then:

```zsh
db.create_all()
```

You should see a file called database.db appear on the left hand side of your screen. Great job!

*If you get an error in your terminal that says "AttributeError: 'NoneType' object has no attribute 'drivername'", use command + d to exit the python editor and run the following:*

```zsh
export SQLALCHEMY_DATABASE_URI=sqlite:///database.db
```

*The reason you're getting this error is because we set the database URI and then never ran our app.py file: meaning the code we wrote to access this URI and pass it to our database was never executed. You'll encounter this error from time to time if you change your URI, move it, or update your Config file occaisionally and then try to create your database without running your app first. It can be a tricky error to debug, which is why I want to make sure you know what it means and what's going on.*

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
from app import db
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

**Don't forget: every time we change our database models we will STILL need to delete our database.db file before re-running our app. If you start running into issues where you don't think your insert or delete statements are working, and you know you've made some changes to your models, delete your database.db file and let your app recreate it for you before further debugging.**




### Stretch Challenges:

1. Create a form to dynamically add users or students from the client side. Use what you know about accessing items from forms to create your object.

2. Create a way to delete items from the front-end (for example, a button).

3. Look into update statements, and create a way to update your students or users' names.

### Submission:

Link this in your tracker if you completed it so that you can get it reviewed! And, as always, if you find any typos, open an issue on the repository (either this one or the project repo). If you need clarification or have questions, come to office hours or send us a slack message!
