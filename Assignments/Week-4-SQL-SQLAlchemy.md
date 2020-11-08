# SQL & SQLAlchemy

#### SQL databases are important to understand how to use, but writing a CRUD application using SQL statements is tedious.

SQLAlchemy is an ORM - an Object Relational Mapper. This means that it takes an object-oriented programming language -- like Python -- and maps objects that we understand to the table structure of an SQL database. This makes it much more intuitive, and this abstraction enables us to much more easily build data driven applications.

#### SQLite vs. MySQL or PostgreSQL

For this tutorial, we will be using SQLite. This is a great way to get started with testing CRUD routes and not dealing with the hassle of a production database. We'll get more into deployment and production databases a bit later.

The primary difference between SQLite and MySQL or PostgreSQL is that our database is actually not going to be hosted on its own server. Instead, it's going to reside in our file system. This is not scalable for larger projects with lots of data (or very secure), but it's easy to set up and avoids the learning curve that comes with installation and proper setup of MySQL or PostgresQL. At the end of the term, I'll create a tutorial for switching over to a production database and deploying your application. For now, let's just focus on understanding SQL & our ORM.

If you're interested in understanding more about *which* production SQL database manager you should choose when you're working on production applications, [this](https://www.postgresqltutorial.com/postgresql-vs-mysql/) article is an extremely helpful resource to compare the two.

#### Let's get started!

1. Clone into the repository:

```zsh
git clone https://github.com/flask-django-independent-study/sql-sqlalchemy-tutorial
```

2. Set up your virtual environment! You all should be good on this, but if you have questions, check online for resources OR check out the Rookie week one Halloween Party assignment for a list of instructions!

3. Install Flask and Flask-SQLAlchemy as well as SQLite 3:

```zsh
pip install flask flask-sqlalchemy sqlite3
```

4. Freeze to your requirements.txt

```zsh
freeze > requirements.txt
```

5. In your directory, check out the example database model. We're going to be writing something like this. But first, we have some setting up to do.

6. Follow the TODOs in the starting code to initialize your Flask application.

7. Now, check out config.py. Notice that something is already there for you, and it should look like this:

```python
SQLALCHEMY_DATABASE_URI = "sqlite:///database.db"
```

This statement sets the database URI (the address of the database we're using) to a sqlite (testing) database, and we told it to create it at "database.db". This is an important environment variable that will tell SQLAlchemy where to insert and query for things. For now, database.db is going to be our database.

8. In your app.py file, make sure you follow the TODOs to initialize your database. This line should look something like:

```python
db = SQLAlchemy(app)
```

You can then provide it with the SQLALCHEMY_DATABASE_URI by configuring your app from object, like we did in the last assignment.

```python
from config import Config
app.config.from_object(Config)
```

9. Now, once you have your app all set up, let's make sure we write the code to run the app at the bottom and get that TODO out of the way before we get too ahead of ourselves, just so we don't forget.

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

12. Now that we've got our database created, let's check out our first model. Go to model_example.py, and take some time to read the comments and look over the model. This is SQLAlchemy syntax, and what SQLAlchemy is doing is letting us create an object that is representative of the table we're going to create in the database. Pretty cool, right? SQLAlchemy does all the hard work for us behind the scenes. New level of abstraction!

13. Go back to app.py, and now that your app is initialized, it should be ready to run. Run your app from terminal, and go to localhost:5000/user

When you visit this route, it should execute the view function: insert_user().

Go back to your IDE, and check out database.db. Do you see a bunch of nonsense in there? Congratulations! You've added an item to your table.

Don't worry about the fact that it's nonsense. It's SQLite's own little language, and we're not really supposed to be able to understand it. When you begin using PostgresQL, there are GUIs that help you actually see your real data. For our purposes today, though, this is just fine to make sure we're correctly adding and deleting things.

14. Your turn! Go to models.py and follow the TODOs to create your own Student model! Then, follow the TODOs in app.py to write your insert route. Don't forget to delete and recreate your database before you try to run this! It won't work otherwise. Stupid SQL.

15. With your new shiny database, go to your new route. Then, go to the user route. Check your database. You should have two lines of nonsense at the bottom now, possibly with some recognizable text that shows you the name of the entries you created. If you see this, you win. You have now created two tables, and inserted a record into each of them.

16. Now, how do you get this information in non-nonsense form? You query for it! Follow the TODOs to run some queries, both filtered and unfiltered.

17. What would a CRUD application be without the ability to delete stuff? (Or update, but we won't talk about that right now, although we will later on.) Follow the TODOs for deleting your users.

Check database.db - did some things disappear? Congrats! It works! Alternatively, run your query, and see if you've removed the users that way. If the query doesn't return anything, you've done it.

18. Troubleshooting: for all of your routes, don't forget to run db.session.commit(). "Session" is something that SQLAlchemy tracks and uses to manage execution. For insert routes, you also always need to run db.session.add(item).

19. Once you've played around with deleting things, you're done! That's great! I hope you have a basic understanding of how to work with SQL and SQLAlchemy, an extremely useful ORM for working with structured databases.

### Stretch Challenges:

1. Create a form to dynamically add users or students from the client side. Use what you know about accessing items from forms to create your object.

2. Create a way to delete items from the front-end (for example, a button).

3. Look into update statements, and create a way to update your students or users' names.

### Submission:

Link this in your tracker if you completed it so that you can get it reviewed! And, as always, if you find any typos, open an issue on the repository (either this one or the project repo). If you need clarification or have questions, come to office hours or send us a slack message!
