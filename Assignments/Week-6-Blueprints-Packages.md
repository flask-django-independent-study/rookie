# Week 6: Packages & Blueprints

## Flask's blueprint & package system is pretty vital.

When working with larger projects, having dozens of routes in one file gets... well, uh, hellish. That's why this week we're going to focus on refactoring our app to leverage blueprints and packages.

Packages v. Blueprints

So, you may have noticed that I've used a few different terms: packages and blueprints. So, are they the same thing? Not quite.

What's the difference?

Packages are one way of beginning to separate your code. Packages are useful for larger projects where you need to work with multiple modules containing classes, utility functions, etc. They help to keep similar parts of your application organized and grouped together. Simply, packages involve splitting your project up into something that looks similar to this:

Online_Store
│   README.md
│   requirements.txt
|   app.py    
│
└───online_store
│   │   __init__.py
│   │   config.py
│   │
│   └───static
│   |   │   styles.css
|   |   |
│   |   │____assets
│   |          somepicture.jpeg
|   |
|   |___templates
│   |      index.html  
|   |
|   |____main
|          routes.py
|          utils.py
|          __init__.py


**A package basically consists of a folder with an __init__.py file.** Notice that our static folder doesn't have this: that's because Flask doesn't need to recognize this as a package. Static files are ones that Flask is not directly running, such as CSS or JavaScript that are executed by the browser, or assets like images that Flask just doesn't need to run directly.

Using packages, you would leave important files like your .gitignore and your app.py in the project root, as well as your README.md and a few other important files. Within the "big" package folder (in the above example: online_store), you would keep your main `__init__.py`, config.py, and python module files (sometimes broken into separate folders for clarity), with subfolders for static files like assets and styles, and a subfolder for templates (if you're directly serving HTML).

**Blueprints are a way of segmenting your routes for clarity.** In a larger project, it's helpful to NOT have all of your routes be denoted as "app.route(route)". For example, blueprints would encapsulate "main" routes: your home page, about page, and maybe contact page, separately from your "admin" routes, and keep those separate from your "user authentication" routes. This helps with readability and code maintenance.

For some clarity, look at how the below code example sets up an "admin" blueprint. Now, the admin routes are denoted with "admin.route" -- this makes it much easier to scan your code and know what each route is doing without needing to read the whole view function or add tons of comments.

```python
from flask import Blueprint

admin = Blueprint("admin", __name__)

@admin.route('/add-products')
def add_products():
  """Allow admin to add product to catalog."""
  pass
```


While you COULD use just blueprints with a giant routes.py file (similar to what we've been doing, throwing all of our routes in our main/routes.py file), or just use packages by themselves to separate your code into different folders, combining blueprints and packages enables us to properly sandbox files and folders in our Flask applications.

This improves our ability to maintain, read, and work on our code with others. The practices that you'll see in this project, such as having separate folders for different groups of routes, become helpful when there are separate utility functions, class modules, and configuration files for each different "group".

Consider the example of an online store: you have "main" routes, that contain your "home", maybe "about", and "contact" pages. Then, you have "product" routes, that encapsulate routes for viewing product details as well as your whole catalog. For these, you probably also need files that contain utility functions for filtering products, or classes that contain important product attributes. Then, you likely have "user" routes, maybe even further divided into "customers" (which would need class modules and maybe utility functions), "admins", and "superuser" routes.

Whew! We didn't even talk about your cart or checkout! This can get really confusing very quickly in a large project like this.

We can eliminate TONS of confusion and scrolling up and down in our code editor by separating functional aspects of our application into packages, and using blueprints to clarify what routes are for what part of your application. We've actually already been doing this: remember when I told you that our main/routes.py file was utilizing *Blueprints?* This is what that meant. So now, all we have to do is break up the rest of our code to utilize this model, and make it even cleaner.

Let's get started!

If you haven't already, check out your [resources](). We've linked great resources for blueprints and packages there. Once you feel ready, get back here and complete the following TODOs:

1. We're going to need to add a few new folders and files to our application. This week we won't be cloning into a new repository, instead we're just building on our finished application from last week. If you haven't finished weeks 4 or 5, you'll need to go back and complete these assignments before working on this one.

I want you to check out [this video](https://drive.google.com/file/d/1uvHHr-HgOFRoBLJVbNwkakuwgvNQbhnN/view?usp=sharing) to see what the end file structure will look like. Alternatively, here's a screenshot to demonstrate:

![Screenshot of file structure](assets/week_6_file_structure.png)

Now that you have an idea of the end result, let's get started! Add the following folders inside events_app:

    1. holidays
    2. user
    3. admin

Inside each folder, create a new file called `__init__.py`. This is the file that tells Flask that these are packages and to look inside them for routes and other important executable things, like functions.

Leave the `__init__.py` file within each folder empty. We won't be working with these. **DON'T CHANGE ANYTHING IN OUR ORIGINAL `__init__.py` THAT IS OUTSIDE OF OUR PACKAGE FOLDERS**.

Great! Now, inside each of your new folders, add a `route.py` file. Leave this blank for now as well, but we'll be filling these in later. Some of our folders also need a utils.py file, but we're going to wait on this until we know which files are going to need to have corresponding utility files.

2. First, let's work on refactoring our admin routes. We're going to take all of our routes that start with '/admin' and we're going to cut them. Next, we're going to go paste them inside 'admin/routes.py'.

Awesome! If you're using a linter, you probably see a TON of errors. This is because we haven't imported all of the packages and modules that we need for this route. Python also now doesn't know what to do with our decorator, because we haven't told it where to import it from. Let's go ahead and add in our import statements:

```python
"""Package & module import."""
from datetime import datetime
from flask import render_template, redirect, url_for, Blueprint, request
from events_app import db
from events_app.models import Event
from events_app.admin.utils import admin_required
```

One of these imports will throw an error if we don't do something. Which one do you think it is?

Awesome, it's the last one: `from events_app.admin.utils import admin_required`. Do you know why this would throw an error?

We're attempting to access this filepath: 'events_app/admin/utils.py' and import a function. The thing is, we don't have a utils.py file yet. Let's go ahead and **inside the admin folder** create a file called utils.py.

Now, let's go back to our main folder, and go to its utils.py file. We're going to **only** take the admin_required function that we wrote and paste it into our admin/utils.py file. Next, we need to make sure that we also cut and paste our corresponding imports. All of the imports at the top of main/utils.py should correspond with this function. You can cut and paste all of these. For reference, this file should now look like:

```python
"""Import packages for helper functions."""
from functools import wraps
from flask_login import current_user
from events_app import login_manager

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

Awesome. Our admin routes should be good, right? Not so fast! This won't run yet. Do you know why?

3. We haven't changed our admin routes from main.route yet! We also haven't *registered our new blueprint*. In the import statements I gave you for /admin/routes.py, you might notice that I imported something from Flask called *Blueprint*.

We're going to need to instantiate an object of the Blueprint class and then make sure that we name our routes accordingly. I want you to go back to main/routes.py and look at the top of the file. You see something there like this:

```python
main = Blueprint("main", __name__)
```

and then our routes are named accordingly:

```python
@main.route("/")
def homepage():
    """
    Return template for home.

    Show upcoming events to users!
    """
    events = Event.query.all()
    return render_template("index.html", events=events)
```

So, we're going to need to follow this pattern for our admin routes, too.

At the top of the admin/routes.py file, add the following statement:

```python
admin = Blueprint("admin", __name__)
```

Then, change all of your route decorators to `@admin.route()` instead of `@main.route()`.

Once you've done this, we're going to need to go to our main `__init__.py` file.

4. Inside `__init__.py`, because we already were registering our *main* blueprint, you already have a few things in `__init__.py` to demonstrate what we're going to need to do for all of our new packages & blueprints. The existing lines in this file look like this:

```python
from events_app.main.routes import main

app.register_blueprint(main)
```

Can you use this to deduce what we're going to need to do in order to register our new admin blueprints?

That's right! Just follow the pattern:

```python
from events_app.admin.routes import admin

app.register_blueprint(admin)
```

Make sure that you're not removing the original lines that register our main blueprint! We're still going to need that.

What this is doing is saying:

`from MAIN app folder/admin folder/routes.py, import blueprint name`

Then, it's calling register_blueprint() on our app, taking our new blueprint name that we've just imported as an argument.

That's it! You've successfully refactored your admin routes. Now, I want you to repeat these steps for user and holidays, paying special attention to utility functions and helper variables that we're going to want to sandbox intuitively with their corresponding routes.

    - user routes:
        - login
        - register
        - user (dummy profile page)
    - holidays:
        - holidays (about_page)

When you're working through this, pay special attention to variables and helper functions you may need for the given routes.

**Make sure that your routes.py files ONLY have routes & view functions in them**

All other global variables or functions that aren't view functions should be in the utils.py file for that folder.

A good practice is, once you've refactored one chunk into its own folder and files, run your app and do some testing. Debug before moving on. If you refactor everything at once, it's a much longer process to debug and figure out where issues are.

5. One really important last step is to go through and make sure that we're properly referencing all of our routes in our redirects and in our templates. If you get an error that says "Cannot build url enpoint for...." that means that somewhere in your code you're improperly referencing a view function.

For example, in our base.html template, we're using Flask's url_for function to set our navigation links. All of these will now need to be changed except for main.guests, main.rsvp, and main.homepage.

Make sure to double check all of our redirects as well. Anywhere you use url_for(), you're going to want to check things out and make sure you're providing the correct `blueprint name.view function name`.

6. Lastly, you're going to run into a few naming errors. Once you name your blueprints user and admin, our view functions that are just called 'user' and 'admin', respectively, are going to need to change. I simply changed mine to 'user_page' and 'admin_page' so that my blueprints and folders could have the most intuitive names possible.


### Wrap Up:

I want you to try to complete as much of this week as possible on your own. Because I know refactoring can be a pain and that sometimes hearing and seeing things is much more helpful than just reading, I've included [this video]() - it's a full walk-through you can follow along with to check or work or get un-stuck if you need it.

*I made one error when I was walking through, where I said that this syntax*:

```python
from events_app.folder.file import function
```

*syntax is specific to Flask - that's not true, it's totally a Python thing and I misspoke.*

Here are some FAQ's that hopefully help as well:

### FAQ's:

**Q: Why do I need an __init__.py file in each folder?**

A: For Flask to recognize a folder as a package, you need an __init__.py file. It's okay if it's blank, this file just tells Flask to look into this folder. Your static folder, for example, doesn't need this, because Flask looks for static for different kinds of files.

**Q: Why can't I just have a route/ folder with a main.py and party.py? Why do I need seperate folders? Is this just best**
**practice?**

A: You could just have a folder with these blueprints! The reason that we're separating these into separate folders is to get you used to using packages and blueprints. This is helpful in larger projects to sandbox your routes, utility files, and class modules into folders of "like" things. It makes sense to keep the user route file in the same folder as the user utils.py, and the module that you use to define your User class.

**Q: On a larger project is it bad to have a template folder in each blueprint folder?**

A: This is not a bad idea! This is a super great way to further clarify and categorize your files in your project, to keep yourself and your team sane when handling lots of templates, routes and files! I would just recommend looking into how Flask looks for templates so that you can avoid things getting buggy if you need to move things around: https://python-adv-web-apps.readthedocs.io/en/latest/flask3.html#folder-structure-for-a-flask-app https://stackoverflow.com/questions/13598363/how-to-dynamically-select-template-directory-to-be-used-in-flask#13598612

**Q: Is it best practice to use a url prefix for all blueprints or can use one only when I think it's necessary?**

A: This is really up to you, your team, and your project to decide what's best. You can use url_prefix as you see fit when you register your blueprints!

**Q: Is it best practice to have the app start using init.py?**

A: First I want to clarify what you mean by "start". In __init__.py we "initialize" the Flask application. This means that we create our app:

app = Flask(__name__)
We want to run our app in our app.py folder, which, if you remember, should stay in your project's root folder. Running the app looks like this:

if __name__ == "main":
  app.run()
So yes, it is best practice to initialize the application in __init__.py. However, it's best practice to run the app from app.py.

**Q: Is it possible to store @app.error_handler() routes inside their own blueprint?**

A: It is totally possible, and not a bad idea! You'll want to make sure that the folder you store these in (if you have them in a separate folder), contains an __init__.py file, and that you're registering the blueprint correctly. For clarity, you could call the blueprint something like "error" so that your route would look like: @error.error_handler(). This will help you debug and avoid naming issues or confusion.

If you don't see your question here, feel free to contact Starlight or Sid via Slack.

If you find a typo, or other issue with the repository, please add an issue so that we can address it!

Thank you for your feedback!
