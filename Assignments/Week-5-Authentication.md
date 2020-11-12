# Authentication with Flask-Login

## Authentication is an important part of most applications.

Currently, in our application, any user can just add, delete, and edit events to their liking. This isn't great, for a lot of reasons.

From your social media applications to school Slack, to websites you use every day that you might say "Why do I even need a log in for this site?!": user authentication is everywhere. Even sites that *you* never log in to likely have authentication on the back end to restrict access to certain pages to only admins or owners of the site. Tracking users and restricting access are two key tenants of modern applications, and you'll be using these constantly in your own work.

### The tools:

For authentication, we'll be using Flask-Login. This is an extension of the Flask ecosystem that makes it easy to track users using something called *sessions* (more on this later).

Because we're also going to want to define an admin *role*, we're going to learn a little bit about a concept called **decorators**, and even write one ourselves.

### Some background:

Flask Login is an extension of Flask that provides user session management. It does this using a few different tools: tracking a current_user object, cookies, and a user loader defined by us that tells the extension where to get our user object from (i.e: which database manager - in our case, we will define a method to query our user from our SQL database).

You don't need to worry too much about what Flask-Login is doing behind the scenes. Just to further explain, though, it's using some server magic and some browser storage methods (cookies) to keep track of which users are logged in. While in our application, it may seem like "current user" can only be one user at a time, since it uses browser storage Flask-Login is actually able to track all current users of our application at once. This will make a bit more sense when we get into the code.

### Enough talking, let's get started!

First, you're going to want to clone the starter code:

```zsh
git clone https://github.com/flask-django-independent-study/rookie-events-week-5/blob/main/events_app/main/routes.py
```

Next, don't forget to set up and activate your virtual environment. You're then going to want to run:

```zsh
pip3 install -r requirements.txt
```

And then reactivate your virtual environment!

1. First, before we look at what we've installed that's new and start writing some code, we're going to want to add something called a secret key to our .env file.

A secret key is something that Flask-Login uses for, well ... secrets. Anything that uses encryption will require this key to be set. In this case, that "thing" is sessions.

[This](https://stackoverflow.com/questions/22463939/demystify-flask-app-secret-key#22463969) is a great explanation on StackOverflow that goes into some more detail about why this is necessary. But for those of you who want to stay here, just think of this as a key (programmers are bad at metaphors or something) - it enables us to "lock" things up, and it's used to de-crypt or unlock these things. Make sure this only stays in your .env file, because although this is just a little dummy project, anyone with this secret key would be able to decrypt anything in your application. It's good practice to ALWAYS ALWAYS ALWAYS keep this secret.

In your .env file, add the following line:

SECRET_KEY=yoursecretkeygoesherewithoutquotes

Of course, you probably don't want your secret key to just be words. I use something like [this](https://passwordsgenerator.net) to generate mine sometimes. You can generate a super long string of nonsense that would be very very hard to break or figure out with brute force. This is a good place to start.

2. Now that you've added your secret key, go to your config.py file. We're going to need to add another line there to make sure that our app can access our secret key at all.

Add the following line in place of the TODO:

```python
SECRET_KEY = os.getenv("SECRET_KEY")
```

We've kind of done this kind of thing before, but if you have questions about what we're doing in this file, refer to your week three and four assignments.

3. Next, let's navigate to our `__init__.py` file. We're going to need to add a new import statement, and a few more lines to get Flask-Login properly set up.

Flask-Login uses a class called LoginManager to do some things behind the scenes. I won't get too deep into this, but I'll link the official Flask-Login docs in your resources.

We're going to need to import LoginManager from flask_login. I trust that you all know how to write import statments by now, so I'm going to send you off on your own on this one.

Underneath your line that initializes your database as db, you're going to need to add another line that does something very similar. You're going to need to set a variable login_manager equal to the result of instantiating LoginManager() with app as a parameter. Try this on your own before looking to the following line.

```python
login_manager = LoginManager(app)
```

Next, we're going to need to access a property of login_manager called login_view. We're then going to set this to be our 'login' route. What this does is redirect a user that attempts to access a protected route back to the login page to first login. Because we're accessing login from a blueprint, we're going to set the login_view property of login_manager equal to "main.login". Try this on your own before looking to the following line.

```python
login_manager.login_view = "main.login"
```

Nice! That's all we need to do in `__init__.py` to set up Flask-Login. So far so good!

4. Next, let's move on to our models.py file. In there, you'll find some more TODOs, and we'll walk through them step by step. Before we even go ahead and write our User model, we're going to have to write something called a user_loader. Since Flask-Login doesn't enforce any one type of database, we have to tell it how to retrieve our users.

Quick aside: I struggled with the decision to place the user_loader in this file. It really doesn't go here, and I don't like this placement. We'll be moving this later, when we work on blueprints and packages next week, but for now it can be here and be a bit messy.

As I have been above, I want you to try to write this yourself before referencing the code snippet that I'll include below.

First, you're going to need to import login_manager from events_app at the top of your file. You can do this right next to where you've already import db. This gives us access to the decorator that we're going to need above this function. *If you don't know what a decorator is, that's okay. You've actually been using them all along, and we'll get into them in much more detail in a little while, and even write our own.*

Then, the function needs to have this right above it (similar to how we put `@app.route('/')` above our view functions.):

```python
@login_manager.user_loader
```

On the next line, define your function. You can name it anything you want but I named mine "load_user()" if you can't think of how to name it. This function needs to take a user_id as a parameter, and query the database for this user by id. Special hint: SQLAlchemy has a method called .get() that enables you to get just one item from the database by id. You're going to need to return this queried user.

Were you successful? The code should look like this:

```python
# Set user loader for Flask-Login


@login_manager.user_loader
def load_user(id):
    """Define user callback for user_loader function."""
    return User.query.get(id)
```

If you used query and then .filter_by instead, that's totally fine. Just make sure you only access the .first() item with that id for good practice.

5. Now we need to write our User model. This is going to be a database model that inherits from one other class that Flask-Login gives us. You'll need to import this other class at the top. So, from flask_login you'll import UserMixin. When you define class User, you'll need to inherit from db.Model and UserMixin. You can add these both to the class arguments separated by a comma.

Your User class will need the following properties:

    1. id = type Integer, set primary_key to True
    2. name = type String, I have a max of 40 characters. Set nullable to False
    3. username = type String, I have a max of 40 characters. Set nullable to False
    4. email = type String, I have a max of 30 characters. Set nullable to False
    5. password = type String, I have a max of 120 characters (I'll tell you why later). Set nullable to False
    6. is_admin = type Boolean, set default=False

We're going to add a few additional methods to this class a bit later. For now, this is fine.






3. Add to stretch challenges: enable user to sign in with their username OR email
