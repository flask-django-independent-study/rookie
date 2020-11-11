# Authentication with Flask-Login

## Authentication is an important part of most applications.

Currently, in our application, any user can just add, delete, and edit events to their liking. This isn't great, for a lot of reasons.

From your social media applications to school Slack, to websites you use every day that you might say "Why do I even need a log in for this site?!": user authentication is everywhere. Even sites that *you* never log in to likely have authentication on the back end to restrict access to certain pages to only admins or owners of the site. Tracking users and restricting access are two key tenants of modern applications, and you'll be using these constantly in your own work.

### The tools:

For authentication, we'll be using Flask-Login. This is an extension of the Flask ecosystem that makes it easy to track users using something called *sessions* (more on this later). 
