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



* For later: secret key generator: https://passwordsgenerator.net *

3. Add to stretch challenges: enable user to sign in with their username OR email
