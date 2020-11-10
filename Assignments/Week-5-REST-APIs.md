# REST APIs

You've made calls to an external API (someone else's server) and now it's time to build your own.

## Why though?

It's actually very rare that you're going to build an application that serves static HTML like we have been. While Jinja and other templating engines enable us to add some dynamic content, this quickly becomes a chore, become limiting, and can provide a poor user experience. I'm not sure if you've noticed, but we're reloading the page every time we submit data via a form or click a button. In contrast, sites that you visit frequently certainly don't force a reload each time you input some information. Why do you think that is?

The smooth flow of most modern websites is largely due to the fact that most of these websites handle transitions and things on the front end rather than on the back end. Their back end code is responsible for providing the data that the front end needs to perform tasks, and that's about it. This means that while the back end is responsible for inserting, querying and deleting things from the application's database (as well as sometimes performing computations or other "heavier" tasks), the front end is doing... just about everything else.

In a data driven site that utilizes a Flask or Django back end, it's likely that the front end would utilize a front end framework such as React or Vue to really improve reactivity and a smooth user experience.

That's not entirely necessary, though. For our application, we'll be using vanilla Javascript and a Javascript library called Axios to make requests to our back end. We will then need to refactor all of our routes to return data that our front end can understand, and refactor our HTML to not use templating, but to rely on Javascript instead.

Whew. We've got a lot of work ahead of us this week. First, though, I want to make sure to clarify a few more things so that you're well-informed as we move forward.

### What does REST stand for?

REST stand for representational state transfer. It's just a fancy way for saying the server can CRUD (create, read, update, and delete) database resources. The core idea is that every server url serves as an access point to the database resources.

### REST API vs GraphQL

You may have heard of GraphQL, which is another type of API. GraphQL is a bit more involved to set up so we'll stick to REST for now. If you would like to learn more about the differences there is a video in the resources page.

### API Endpoints

In the project you'll see me write "endpoint". An endpoint or API endpoint is just a route that the front-end can make requests to and get json data in return.

## Let's get started!

1. Clone the repository:

```zsh
git clone https://github.com/flask-django-independent-study/week-5-events-app
```

2. 
