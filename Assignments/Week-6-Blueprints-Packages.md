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


******We're going to need to change the below instructions when I finish refactoring source*****************

Set up your file structure. You'll need an app.py file, and a main project folder to start (I like to name mine either server/API if I'm building an API, or the project name if I'm just serving HTML). For the sake of consistency throughout the instructions, let's call this folder "week_one_blueprints"

It's good practice to keep app.py (the file that runs your app) in your project's root directory. Obviously, it can work if you move it, but it can cause problems, since Flask looks in the project's root for the app. For now, let's keep it in our project's root.

This project is going to stay relatively general, and we'll be creating lots of dummy routes to start. Once you're done, feel free to go refactor a previous project to use blueprints, or add more to this as a stretch challenge. For now, create an __init__.py file in your week_one_blueprints folder. Then, add a config.py file.

Initialize your Flask application in __init__.py. Also, make sure you import Blueprint at the top of the file. We'll need that in a minute.

In your project's root directory, create a .env file, and add a SECRET_KEY. We don't really need this in this application, but I want you to get practice with the whole setup.

In config.py, create class Config. See your resources for proper setup. This class doesn't have an init method, it just has attributes. Your Config class should look something like this for now:

class Config:
    """Set environment variables."""
    ENV = "development"
    SECRET_KEY = os.getenv('SECRET_KEY')
    DEBUG = True
In __init__.py, create your app. Set app.config.from_object(Config) so that your application can use the environmental variables you just configured. Now, the rest is going to be pretty independent, but this is what we need:

Create a "main" folder. This folder has to have an __init__.py file in it, but it's okay to leave that file empty. This file just tells Flask that we're using packages, and to recognize this folder as a package to look in. Also in this folder, create a routes.py folder. At the top of routes.py, import Blueprint, and initialize your "main" blueprints. Add any three routes serving any basic HTML files in this file.
Create a "party" folder. Follow the above instructions to initialize, and make sure you have at least two routes in this one. Don't forget to create your templates to go along with these!
In your main __init__.py in your project root, make sure you're registering your blueprints so your app knows what it's looking for. And, don't forget to test!
If you run into problems with things running, check these few things:

Make sure that in each folder, you're starting your routes with "/blueprintname/routename" - OR that you're adding URL prefixes when you register your blueprints (this is a stretch challenge, if you don't know what this means or how to do this, don't worry! (or Google))
Check your file structure, the locations of your templates, and your resources. They're there for a reason! We trust that you guys mostly know what you're doing, you got this!
If all else fails, we'll be hosting office hours each week to help debug and check things out. You can Slack us anytime with questions and we'll get back to you as soon as we can.
