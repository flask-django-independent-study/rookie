# APIs

### What is an API?

APIs are an extremely important concept to understand. This is something that you will need to know how to utilize as a developer, and especially for those of you going into back end web development, something you will eventually need to learn how to build.

(SPOILER: we'll be learning how to build a RESTful API in about two weeks.)

API stands for Application Programming Interface. I think it makes the most sense to think about APIs in super practical terms: they allow you access into parts of someone else's server without exposing the whole thing. For companies and organizations, exposing their server would be... big no no. However, many companies have valuable data that keeps you from reinventing the wheel that they want you to be able to access (sometimes for free, often.. not for free).

This means that you, as a developer, are able to access valuable data and information that other people have gathered or calculated or programmed for you. It would be pretty ridiculous to need to have access to teams of meteorologists, analysts, equipment, etc just to display some weather information on your application, right? This is a great example of something APIs are really good at: accessing data.

For this assignment, we are going to use an API that doesn't require authorization and is easy to use to get the hang of making requests.

**Before we get too deep into writing the code to access API endpoints**, I want you to install a program called Postman. We're going to use this to make our first API calls, and solidify the concept.
If you have Postman, you're good! If Postman is the guy that delivers your mail, you're going to go [here](https://www.postman.com) and sign up for a free account. Once you're done, you should download the desktop client. This is something you'll use for classes and once you start writing your own API endpoints, it's extremely useful for testing. Get the desktop app, it's worth it.

Once you have the desktop app open, you should see a page like this:

![Postman Screenshot](/assets/postman_screenshot.png)

Now, in the url field, you're going to paste this url: http://ipwhois.app/json/8.8.4.4 and change the request method on the left to POST.

Hit send to send the request. What do you see? You should see something like this:

![IP Whois return data](/assets/return_data.png)

Congratulations! You just made your FIRST request to an API endpoint! This one is giving you location information for a test IP address using the IP Who Is API.

### Let's GO!

Now, you're going to go [here](https://calendarific.com/?ref=public-apis). We're going to integrate data for our application from an API that allows us to access all holidays from any country in the world. Pretty cool, huh? Since it's after Halloween, I think it's time to turn our Halloween Party app into something a bit more robust and useful.

Scroll down and sign up for the free plan. Once you've signed in, you'll see a page like this:

![Calendarific dashboard screenshot](/assets/calendarific_dash.png)

I have my API key hidden because those are SECRET! That means we also need to learn how to handle secret files in our applications. You're really learning a lot of good stuff today.

Go to your project root folder, and create two new files. One is going to be called .env, and the other .gitignore.

Inside the gitignore file, I want you to add two entries. It can just look like this:

```zsh
.env
env
```
What this does is tells git to ignore these files, so they aren't pushed to your public repository. DON'T COMMIT YOUR SECRETS!

Inside your .env file, add the following line:

```zsh
API_KEY=yourapikeyhere
```

Remember, your API key can be found in your account dashboard on calendarific.

Now that you have your API key, let's test a call in Postman. In the Postman URL field, make a GET request to this URL:

https://calendarific.com/api/v2/holidays?&api_key=yourapikey&country=US&year=2019

Don't forget to replace 'yourapikey' with your ACTUAL API key.

What you should see is a list of all of the Holidays in the US for 2019. Super cool! We're going to use this now.

Go to your terminal and clone the starting code. We're still building on the same project, but I added navigation and some super basic styles. **For better practice, compare my code to yours and type in the new stuff. I really only changed up the templates. This will help you remember more of what you're doing than copy and pasting**.

```zsh
git clone https://github.com/flask-django-independent-study/rookie-events-week-3
```

Don't forget to set up and activate your virtual environment and install requirements!

Now, we're going to go ahead and overhaul your about page. We aren't going to do anything too fancy just yet, but we're going to make sure that we show the holidays for the current month to our user.

First, we need to make sure we can access our API key in a secure way. At the top of your app.py file, add the following:

```python
import os

API_KEY = os.getenv('API_KEY')
```

Also, we should probably let our application know what URL we're going to be using. For now, we're just going to need this in our about route, so I think we can make this a local variable to keep things clean.

```python
@app.route('/about')
def about_page():
    """Show user party information."""
    # Sometimes, a cleaner way to pass variables to templates is to create a
    # context dictionary, and then pass the data in by dictionary key

    url = 'https://calendarific.com/api/v2'

    context = {
        "date": "10/31/2020",
        "time": "10:00 pm"
    }
    return render_template('about.html', **context)
```

You may have noticed, that the url I gave you above to get all US holidays was... a bit longer. This is because we have to give request *parameters* to get the specific information we need.

In order to get JUST the holidays for this month, we're going to need to do a few more things.

First, we need Python to be able to dynamically update the month. This really doesn't do us much good if we need to change our source code every time we switch months, right? Lucky for us, Python has a library for that. At the top of your app.py file, add the following import:

```python
from datetime import date, datetime
```

Then, let's figure out what day today is, and convert it to a string so that we understand what we're looking at:

```python
today = date.today()
# Now, let's just get the month as a number:
month = today.strftime('%m')
# Now, let's get the current year:
year = today.strftime('%Y')
```

Alright, so we have what we need so far. Now, how do we parse the response?

Well, the response is going to return JSON. However, it's going to be a bit ugly and hard to read. Let's add one more import to help with that:

```python
from pprint import PrettyPrinter
```

Now, let's work on our about page. We're going to need to make a request.

Before we get started, let's add one more package to our project:

In your terminal, in your project directory (and with your virtual environment activated!):

```zsh
pip install requests
```

Then,

```zsh
freeze > requirements.txt
```

At the top of your app.py file:

```python
import requests
```

This is a module built into python that enables HTTP requests. This is a simple way to make an HTTP request to an external url.

I'll get you started with a screenshot from a different project. I want you to take this information and use it to make a request to our current URL:

*HINT: Remember, we're getting all of the holidays for the current MONTH and YEAR for our current country.*

![Example api request to url in python](/assets/example_req_route.png)

In the above example, we are accessing data from a form. Since you aren't accessing data from a form in this route, we probably don't need that, right?

Then, it looks like we get the url from somewhere. You should already have that in your route, so you're covered.

It looks like after that, a dictionary is defined with parameters. Remember what we said about the request needing parameters? Use the documentation [here](https://calendarific.com/api-documentation) to figure out how you need to name your parameters. The key names have to be specific, otherwise the API won't know what to return to you.

*I could tell you all of the specific parameters you need for this, what to call them, and how to format everything, but I don't think that would be as helpful. Learning how to read documentation and find specific information is going to be a big skill for us as programmers, and I want you to get some experience doing this.*

We then store the result in a variable, and access data from the resulting response. Notice that we access values in our JSON data just like in a Python dictionary!

Remember that *context* is just a dictionary we're passing to our *template*. This is what the template needs to display dynamic information.

Before we worry about what data our template needs, I want you to get familiar with the response. Store it in a variable, and print that variable.

We probably want to show our users a list of holidays, the date that they occur, and a brief description. Take some time looking through the data and see if you can find these values.

Once you feel comfortable, access these values and pass them into context, like this:

```python
@app.route('/about')
def about_page():
    """Show user party information."""
    # Sometimes, a cleaner way to pass variables to templates is to create a
    # context dictionary, and then pass the data in by dictionary key

    # TODO: your request code goes HERE

    # TODO: access name, date, and description for all holidays this month. HINT: you likely need to loop through the holidays in the data

    holidays = []
    # HINT: perhaps holidays shouldn't be an array, but something that can store key-value pairs?
    # Think about the format we receive the response in
    dates = # TODO: access data from your response
    descriptions = # TODO: access data from your response

    #  TODO: access the data we need from holidays, and make sure we can loop through this information in our template

    context = {
        "holidays": holidays,
        "date": dates,
        "description": descriptions
    }

    return render_template('about.html', **context)
```
Now, go into your about template, and refactor it so that we can show the users this data! Feel free to use a list, headings and paragraphs, whatever you feel looks best!

### Congratulations!

You have now been introduced to APIs and making calls. You've used two separate APIs to get data and show it to your users. You've learned the basics of Postman, an extremely helpful application that helps you test API endpoints, and you've gotten more comfortable (hopefully) with reading API documentation and extrapolating data!

#### Stretch Challenges:

1. Make some more requests: think of other routes you could build or other information you could show your users. What else might be helpful? Should users be able to RSVP to events that correspond with holidays?

2. Make it pretty! This is always a stretch challenge because while a real working application is the most important, we want our portfolio projects to look great, too.

3. Refactor the application to make some more sense! What if the holidays show up on the homepage, and the about page leads to some details about each holiday? 🤔
