# Authentication with Flask-Login

## Authentication is an important part of most applications.

Currently, in our application, any user can just add, delete, and edit events to their liking. This isn't great, for a lot of reasons.

From your social media applications to school Slack, to websites you use every day that you might say "Why do I even need a log in for this site?!": user authentication is everywhere. Even sites that *you* never log in to likely have authentication on the back end to restrict access to certain pages to only admins or owners of the site. Tracking users and restricting access are two key tenants of modern applications, and you'll be using these often in your own work.

### The tools:

For authentication, we'll be using Flask-Login. This is an extension of the Flask ecosystem that makes it easy to track users using something called *sessions* (more on this in a bit).

Because we're also going to want to define an admin *role*, we're going to learn a little bit about a concept called **decorators**, and even write one ourselves.

### Some background:

Flask Login is an extension of Flask that provides user session management. It does this using a few different tools: tracking a current_user object, cookies, and a user loader defined by us that tells the extension where to get our user object from (i.e: which database manager - in our case, we will define a method to query our user from our SQL database).

You don't need to worry too much about what Flask-Login is doing behind the scenes. Just to further explain, though, it's using some server magic and some browser storage methods (cookies) to keep track of which users are logged in. While in our application, it may seem like "current user" can only be one user at a time, since it uses browser storage Flask-Login is actually able to track all current users of our application at once. This will make a bit more sense when we get into the code.

### Enough talking, let's get started!

First, you're going to want to clone the starter code:

```zsh
git clone https://github.com/flask-django-independent-study/rookie-events-week-5/blob/main/events_app/main/routes.py
```

**Next, don't forget to set up and activate your virtual environment (see previous assignments if you don't remember how to do this).** You're then going to want to run:

```zsh
pip3 install -r requirements.txt
```

**And then reactivate your virtual environment!**

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

3. Next, let's navigate to our `__init__.py` file. We're going to need to add a new import statement, and a few more lines to get Flask-Login properly set up.

Flask-Login uses a class called LoginManager to do some things behind the scenes. I won't get too deep into this, but I'll link the official Flask-Login docs in your [resources](https://github.com/flask-django-independent-study/rookie/blob/master/Resources/Week-5.md).

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

First, you're going to need to import login_manager from events_app at the top of your file. You can do this right next to where you've already imported db. This gives us access to the decorator that we're going to need above this function. *If you don't know what a decorator is, that's okay. You've actually been using them all along, and we'll get into them in much more detail in a little while, and even write our own.*

Then, the function needs to have this right above it (similar to how we put `@app.route('/')` above our view functions.):

```python
@login_manager.user_loader
```

On the next line, define your function. You can name it anything you want but I named mine "load_user()" if you can't think of how to name it. This function needs to take a user_id as a parameter, and query the database for this user by id. Special hint: SQLAlchemy has a method called .get() that enables you to get just one item from the database by id. You're going to need to return this queried user.

Were you successful? The code should look something like this:

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

Try to write this model yourself before looking at the following line!

```python
class User(UserMixin, db.Model):
    """
    Define User class for Flask-Login.
    We also use this to persist our users in our database.
    """

    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(40), nullable=False)
    username = db.Column(db.String(40), nullable=False)
    email = db.Column(db.String(30), nullable=False)
    password = db.Column(db.String(120), nullable=True)
    is_admin = db.Column(db.Boolean, default=False)
```

6. We're going to go to our main/routes.py file now, and look at the TODOs. We're going to implement logging in and out before we worry about admin roles or password hashing.

**Don't forget to delete your database.db file and then run your server since we've now changed the database models! If you don't do this, you'll get an error telling you that a user table does not exist if you try to run any operations on your users!**

If you scroll down, there's a new section called User Routes in your routes file. Right now, I'm using comments to separate our routes from one another. Next week, we'll make sure this is a bit prettier.

I've included a few new template files as well. Now that we're familiar with what we're going to need to do with our new routes, I think we're ready to focus on these and write some new forms.

7. Follow the TODOs in your new login.html file to write the code for our login form based on the instructions I've provided.
8. Follow the TODOs in your new register.html file to write the code for our signup form based on the instructions I've provided.
9. It's also a good idea to get acquainted with the other two files I've added: admin.html and user.html so that you understand what the intended result of our new route code is going to be.

Notice that our admin.html file has a lot of the code that used to be in our index.html. This is because we don't want anyone who visits our homepage to be able to add, edit and delete events.

Also notice that we have a little bit of code in our user.html file, and it's going to display some user information, but only if we have a current_user. `current_user` is an object that Flask-Login gives us access to to represent our current user, whether they're authenticated (logged in) or not. If they are logged in, current_user will represent a database object. If they aren't, they'll represent an object that Flask-Login gives us called AnonymousMixin.

There is nothing in our template to filter for a current authenticated user, because in our base route we only show the navigation to this page if the current user is authenticated (logged in). Do you think that this would enable a non-authenticated user to access this route if they typed it into the URL?

*Before we get into our routes, I want us to learn about a super handy Flask tool that we haven't learned yet. It's called the flash() method. It's similar to the alert() method in vanilla Javascript if you're familiar. Instead of a browser popup box, though, it appends something to the DOM. Check out our base template: I've already implemented this for you. At the top of your routes file, add "flash" to the list of imports from flask if it isn't already there. Then, you can show something to the user by calling flash("message I want to show")*

10. The answer to the above question is no, and now that we've fleshed out the rest of our code in our templates, we can turn our attention back to our routes to learn why!

Go back to your routes.py file, and scroll to find your user routes. The first route we're going to look at is our register route. I've given you some if statements to get you started, and I'll outline the logic that we're going to need to step through here to get you started.

    1. First, as you can tell, we accept two methods for this route: 'GET' and 'POST'. If the method is 'GET', we need to be able to show the user the sign up form. If the method is 'POST', that means the form has been submitted and we need to handle the data and sign up a user.

    2. We're also going to want to check to see if a user already exists for the information that's been provided in the form. For example, if a username or email that they've entered already exists, we're going to want to tell them to use something different. For our purposes, we're only going to check for EITHER username or email, not both. I'll let implementing both be a stretch challenge.

    3. IF a user doesn't already exist and the password matches the confirm password field, we can execute code to add the new user.

    4. IF a user already exists for this information, we're going to want to show the user something

    5. IF the passwords don't match (password and confirm password) we're going to want to show the user something

Problem solving is a really important part of coding, so try using the logic I've laid out and the existing if statements to code this out. If you get really stuck, use your [resources](https://github.com/flask-django-independent-study/rookie/blob/master/Resources/Week-5.md) or do some Googling.

If you're completely lost, my completed source code is linked in your [resources](https://github.com/flask-django-independent-study/rookie/blob/master/Resources/Week-5.md), but my hope is that you're able to use the TODOs and groundwork I've given you to begin to solve some of these things more independently!

11. Try testing your signup route! It's okay if it doesn't get from start to finish without throwing an error! My login flow redirects the user to the log in page. If you utilize this flow, then you can test through to the redirect to the log in page, and know that you're ready for the next step.

12. Next, we're going to need to work on our login route. I'll step through the logic with you here, too, and send you to mostly do this on your own!

    1. Similarly to the register route, you'll notice we accept 'GET' and 'POST' requests. If the request method is 'GET', we're going to want to show our user the login form, and if it's 'POST', we're going to want to handle our data and log in the user.

    2. If the request method is POST, we're going to first want to check to see if a user exists in our database for the given username.

    3. IF a user exists, we're going to want to verify that the password that was entered matches the password for the user object returned from our database.

    3. IF the passwords don't match, we're going to need to show something to our user so they can try again.

    4. IF a user does not exist, we're going to want to show something to our user, maybe they need to sign up instead.

    5. IF the user exists and the passwords match, we want to log in the user. Flask-Login gives us an easy method for this, and it looks like this:


        ```python
        login_user(user)
        ```

        This is assuming that user is a user variable set equal to the results of querying for the correct user in our database.

        You'll also need to make sure that you import this method from flask_login at the top of your file if you have not done this already.

        Once you've logged in your user, it would be nice if we could show them something to confirm that they've logged in!

13. We should be logging in users now! I would personally provide a redirect to the home page once a user logs in, but it's up to you where you send them. Make sure you're testing before we get too far along! You should be able to sign up and log in now without errors.

14. Let's make sure our users have a place to go and log out. For this, we're going to write a new route ourselves.

    1. Write route "/user". It will only need to accept a GET request, so we don't need to specify the methods.

    2. Inside user, we just need to render the user template.

    3. Look at the logout route. Do you see how underneath our `@main.route` declaration, there's another thing with an @ sign in front of it? This is another *decorator*, and it specifies that login is required to access this route. Because we specified our login_view in our `__init__.py` file, attempting to access this route without being logged in will simply redirect the user to the login route. Remember the question from step # 9? What do you think now?

    The answer is that no, a user can't access the user route without being logged in AS LONG AS we provide this decorator on top. Make sure the user route that you just created looks like this:

    ```python
    @main.route('/user')
    @login_required
    def user():
      return render_template('user.html')
    ```

    Were you successful? Try accessing your user route now (if log in worked for you, you should still be logged in and able to access this).

15. Now that we can access the user route, and we see that there's a log out button, let's do something with it. Just so that you get the full picture, check out the user.js file in your static folder. I've written the event listener to handle this button click: and it just sets our location to our /logout route. There are some better ways to do this, but we're not really worried about this right now.

    1. In your logout route, write out the code to log out the user.

    2. HINT: this is so easy: Flask-Login gives us another function for this. See if you can look in your [resources](https://github.com/flask-django-independent-study/rookie/blob/master/Resources/Week-5.md) and figure out what it is. Make sure you import this function at the top!

    3. Provide a redirect to the home page once the user is logged out.

    Now, go test your route! If you get errors, try restarting your server if you haven't been. Go to your user page, and click log out. You should be redirected to the homepage, and see the "Log In" link at the top nav instead of the "User" link. Cool!

16. You have officially enabled your users to sign up, log in, and log out (as well as see a little dummy 'profile' page). Amazing!

17. Now, though, I think it's time for us to beef up our security a bit. If you were to query for a user from our database, and print out their password, you would be able to see their plaintext password. This really isn't the best, because it means that anyone who has access to your database (including employees at a company, for example) have access to your users passwords. Ideally, we want these to be secret: even from us!

We're going to be employing something called *hashing* which is a type of encryption. This will ensure that we NEVER store plaintext passwords. It also enables us to verify passwords by reversing the encryption using a *key*.

First, we're going to need to ensure that we have a few things installed, and that we make a few import statements to access a library called *passlib*. Then, we're going to make some changes to our model, register, and login routes to make sure that we're storing and verifying hashed passwords instead of plaintext ones.

18. Check your requirements.txt file and make sure there is an entry called "passlib". If there is not, go to your terminal and run the following:

```zsh
pip3 install passlib
```

and then add this to your requirements.txt file:

```zsh
pip3 freeze > requirements.txt
```

19. Now, go to your models.py file. At the top, there's a TODO under the other import statements instructing you to uncomment the import statement there.

The import statement is importing something called "sha256_crypt" from passlib. `sha_256` is actually the name of the algorithm we'll be using for hashing, which is currently considered unbroken (i.e: no one that we're aware of has broken this encryption algorithm yet) and secure. There are many other encryption algorithms you could use. This one is quite popular and standard. There are a few that are *even more secure* theoretically, but it would really be overkill. This is one you'll see quite often whether you use passlib or bcrypt or another similar library for your projects.

Once you've uncommented the import, we're going to need to make sure that we add two methods to our class. First, we're going to need to add a setter method that encrypts the password before it's even inserted into our database. This one is going to look like this:

```python
def set_password(self, password):
    """Return new user from User class."""
    self.password = sha256_crypt.hash(password)
```

Calling .hash() on sha256_crypt *hashes* the password that we pass as a parameter using the sha256 algorithm. Calling this method on our newly created user object will allow us to encrypt the password before we add the user object to our database.

Go to your routes.py file, and in your register route, after you instantiate your User object, call user.set_password(password). *This assumes that you saved the result of instantiating your User object to a variable called user. If you named your variable differently, you're going to need to adjust this accordingly*.

This ensures that the password is encrypted before we run db.session.add(user).

20. How are we going to verify that the plaintext password entered in our login form matches our user password now that it's hashed? Well, passlib gives us a verification or *decryption* method as well. Go back to models.py and add the following method:

```python
def check_password(self, password):
    """Verify hashed password and inputted password."""
    return sha256_crypt.verify(password, self.password)
```

The password that we pass as an argument to this function is going to be the password we retrieve from our login form. It will verify the plaintext password entered at login with the hashed password utilizing the same algorithm to reverse the encryption. If they match, the .verify() method will return True.

We can simply check, then, if the result of this function is True or False in our login route.

Go back to routes.py and let's implement this!

In your login route, where you're currently checking if the password entered in the form matches your user.password, let's change this if statement a bit. Now, you need to just check IF user.check_password(password) == True. Remember, you can just check if user.check_password(password) and then move on, since Python will evaluate this to False if the function returns False. *If that last line doesn't make sense, let me know and we can talk about this and maybe go over some code examples*.

Once you've changed your login route if statement, it's time to test. **Make sure you delete your database.db file since we've made further changes to the models**. You're going to need to run your server and sign up again.

After you sign up, log in. If it works just like before, congrats! You're now securely hashing passwords!

21. Our last point of business is going to be ensuring that we specify an admin user to add, edit, and delete events. While Flask has some advanced authentication extensions that implement roles for us, we're going to do this one ourself because it's good to understand what's going on behind the scenes, and do things the hard way first. And, bonus, we get to learn about and write a decorator for fun.

22. **Why do you keep saying decorator?** GREAT question! I'll include more resources on what a decorator is in your resources, and I strongly recommend you check these out. However, to explain simply: a decorator is like a wrapper function. It **modifies the behavior of the original function without actually modifying the function itself**.

You've actually been using decorators all along. Those things with @ signs in front of them? Decorators. For example,

```python
@main.route('/')
def show_homepage():
  return render_template('index.html')
```

The decorator is `@main.route()` and it takes an argument just like functions do. It actually takes a few arguments: the route (or url extension we're reaching, which in this case is / which indicates just the root url), and the list of methods that route accepts.

The **function being decorated** is what we call our **view function** or our route function. This is the code that executes when we go to that route.

**So what does this decorator thing do?** In this example, this decorator is accessing our Flask object's route method and telling the function we declare below it (the view function, let's start understanding this terminology) to ONLY execute *when we access that route*. This is why we don't have to explicitly *call* view functions like we do other types of functions. This is what makes them so special.

As you've already seen, *not all decorators have to do with server code*. We've also been using another special decorator throughout this assignment, the `@login_required` decorator. What this decorator does is it tells the function below it that it isn't allowed to execute unless the current_user object that Flask tracks has an is_authenticated property set to true. You can read more about this in your [resources](https://github.com/flask-django-independent-study/rookie/blob/master/Resources/Week-5.md), but the code used to create this decorator is actually really simple.

This brings us to our next task: we're going to actually implement this code ourselves and write an `@admin_required` decorator that works MUCH like the `@login_required` decorator does. This will give us a solid understanding of how to write a decorator, what the Flask decorators are doing, and why we might use these in our code.

I want you to go to the file called utils.py within the **main** folder. In this file so far is the get_holiday_data function that we use in our 'holidays' route.

At the top of the file, we're going to add a few import statements:

```python
from functools import wraps
from flask_login import current_user
from events_app import login_manager
```

Let's talk about these. *functools* is a Python library that gives us a few different tools. The one we're using is called *wraps*. What this is going to do is enable us to write our decorator and then implement it with the cool `@decorator` syntax. We *could* just write a decorator like a wrapper function, and implement it by calling our view function inside of it. But, because this is messy and might confuse others reading your code, we're going to keep things pretty and use *wraps*.

Then, from flask_login we import current_user since we're going to need to use this in our function declaration.

Lastly, we import login_manager from events_app so that we can access a method that it has built in (remember - login_manager is an object we instantiated from the LoginManager class that Flask gives us) called .unauthorized() that will redirect our sneaky non-admin users back to the login page and let them know they have to log in to access this route.

    1. Declare a function called admin_required() that takes *func* as an argument. (func is short for function, since we'll be taking another function as an argument).
    2. Inside the function, add the special decorator `@wraps` to the first line (the decorator tells Python we're writing a decorator. So meta.)
    3. Still inside your admin_required(func) function, and directly underneath the `@wraps` decorator, you're going to declare another function called wrapper. Wrapper's parameters look like this (*args, **kwargs). This is a special way of telling python that this function can take any amount of arguments.
    4. Inside wrapper, add the following logic:

    ```python
    if current_user.is_admin:
            return func(*args, **kwargs)
        else:
            return login_manager.unauthorized()
    ```

    5. Then, outside of wrapper, return wrapper.


Were you successful? The function should now look like this:


```python
# Create decorator for routes that require admin role


def admin_required(func):
    """Decorate route function to check is user isadmin."""

    @wraps(func)
    def wrapper(*args, **kwargs):
        if current_user.is_admin:
            return func(*args, **kwargs)
        else:
            return login_manager.unauthorized()

    return wrapper
```

Nice job! You've written your first decorator. Decorators are a really cool concept, not just in Python but in a few different object-oriented programming languages. They're really valuable to learn and understand because libraries that you use will utilize them, and it's important to know what's going on behind the scenes.

23. We are ALMOST there. I know this has been a long assignment but you're learning so much really important stuff. We're going to return to routes.py and write our last line or two of code.

You just created a decorator: `@admin_required`. The reason you can use this decorator like this is because:

  1. We used wraps to tell Python to use this syntax
  2. Our outside function is called admin_required
  3. We won't have to explicitly pass in a function, because the decorator takes the function declared directly beneath it as its argument.

Go to the routes at the bottom of the user routes and add in the admin_required decorator where your TODOs tell you to do so.


24. We're not quite done yet. How do we know if a user is the admin? We have to do this last thing in models.py.

In models.py, add the following method to your user model:

```python
def set_is_admin(self):
    """Set is_admin to true under certain circumstances."""
    if self.email == "sid@sid.com":
        self.is_admin = True
```

You're going to need to replace `sid@sid.com` with an email address that you choose (you should probably make yourself the admin, even if you use a dummy email address for conceptual consistency).

This is kind of a dummy way of checking that the user is an admin, but the logic is similar to what we would see in a real-world application: we're checking if the user fits a specific criteria. If they do, they're the admin and we set that property to true. If they're not (and by default), we're going to assume they're *not* the admin user.

**Delete your database.db file, and test this out!**

Once you've restarted your server, sign up two new users. One should have your admin credentials, and one should just be random. Then, go ahead and sign in as the admin, and you should see the admin routes are available in the nav bar!

Then, log out, and log back in as the random user. You shouldn't see the admin routes in the navbar. What's more, if you type in the URL for the admin routes, you should get kicked right back to the login page.

If these things don't happen, spend some time debugging!

## Wrap Up

Holy shit. Congratulations! You've just learned basic user authentication with Flask-Login. You've learned about sessions, you've implemented password hashing and learned a bit more about encryption, you've utilized best practices for protecting your own secrets, and you've learned about and implemented decorators -- including one you wrote yourself. You're killing it!


### Stretch Challenges:

1. Allow the user to sign in with their username OR email (using a single form input box)
2. Beef up the user bio!
3. Look into file uploads and add an avatar!
4. Is there anything we're doing that maybe *isn't* the best practice? If you think of or find anything, let me know!

### Submission:

Link this in your tracker if you completed it so that you can get it reviewed! And, as always, if you find any typos, open an issue on the repository (either this one or the project repo). If you need clarification or have questions, come to office hours or send us a slack message!
