# Working with Forms & Flask-WTForms

This week, we're going to introduce you to a really important concept: working with data from forms. Nearly any webpage you go to, you're going to be met with forms. Log-in and sign-up forms, payment forms, and so many others, forms introduce us to a  way to access user-input information, which is one big key to any dynamic web application.

As I'm sure you've noticed, in your Halloween-Party application so far, you've had to just hard-code in your list of guests. This seems a little weird because then what's the point of having a site if your friends have to tell you they're coming, and then you have to change your source code to show anyone else who's going to show up!

All that is about to change. I'm going to teach you how to handle data from forms, as well as the basics of the two most common types of HTTP requests. Then, we're going to take a look at another Flask extension called WTForms which is going to make our life even easier in the future when we're working with more form validation and user authentication.

### First things first, what is that HTTP Request thing?

An HTTP request is a request that is made between a client and a server. This probably sounds really weird. In web 1.0, you've only worked with what we call the "client". This is the part of your code that is handled by the browser, and that is user-facing (hence the name, client). The server is what we're building with Flask. This is our back-end, the end that deals with the majority of our data.

We won't get too too deep into the specifics right now, but for our purposes, since we're directly "serving" (rendering) HTML from our Flask application, our templates are our "client" code.

#### GET vs. POST

There are two types of HTTP requests being made. You've already dealt with one of them, without even realizing it! By default, when you write this code:

```python
@app.route('/')
def view_function():
  return render_template('home.html')
```

this route handles a 'GET' request. This does what it sounds like: it GETS the information from the page. In this case, our client is 'GETTING' any data from that view function and showing it to our user.

A 'POST' request also does what it sounds like: it 'POSTs' data to our back end. This is the kind of request we're going to be working with when we use forms. The data from our form will be sent via a POST request to our app.

Because Flask routes, by default, only accept GET requests, we're going to need to tell it when we need to accept a post request. To do this, you add this addition to your POST routes:

```python
@app.route('/rsvp', methods=['POST'])
def rsvp_user():
  pass
```

> Just a side note, if you haven't already seen the 'pass' keyword, it's basically just telling this function to not execute any code right now, and telling us or any other programmers that we're going to finish writing out this function later.

### Getting into forms

On the client side, I won't be giving you too much detail on how to write a form. I believe that you all have enough experience writing HTML to add a form to your template. If you need a refresher, forms look like this:

```html
<form action='/rsvp' method='POST'>
  <label for="name">Name</label>
  <input type="text" name="name" id="name">
  <label for="email">Email Address</label>
  <input type="email" name="email" id="email">
</form>
```

There are two possibly new things here, though in the opening form tag: the "action" attribute, and the "method" attribute. By default, just about everything uses "GET" requests. You can use a form without specifying POST, although it doesn't send the data to the server securely. We're going to start using best practices right off the bat, and all of our forms are going to send data via POST requests.

The action attribute specifies **which route gets the data**. This will be important later.

You may also notice the name attribute in each input field. We'll need this to access the data from the form, so make sure you're including these along the way.

### Let's get started!

1. Right now, I think our biggest problem is that we aren't letting users RSVP on our webpage. Let's change this. In your 'rsvp' template, I want you to add a form to the body. Make sure the opening form tag has the following attributes:

```html
<form action="/guests" method="POST">
  <!-- TODO: Your inputs will go here. -->
</form>
```
You also need to get the following information from guests. Input type is specified before the information required:
> For more information on forms and input types, check out: https://www.w3docs.com/snippets/html/input-types-for-html-forms.html

    1. text: the guest's full name (required)
    2. email: the guest's email (required)
    3. text: the guest's +1's name, if any.
       The guest might not have a +1, right? Let's make this field optional for them.
    4. tel: the guest's phone number
    5. text: the guest's costume description

2. Alright, so we've got a form. Double check that you have a submit button, and make sure that **every** input field has a **name** attribute. This is super important: we'll need this to access the information from the form. I'll be referencing these in the following ways, you can use this as a guide for setting the name attribute:

    1. Full name: name="name"
    2. Email: name="email"
    3. Guest's plust one: name="plus-one"
    4. Phone number: name="phone"
    5. Costume description: name="costume"

3. So, first, let's go ahead and test our form. Run your app, and go to localhost:5000/rsvp. Check out your form, and make sure that you're able to input information in and click "submit". Now, check your url: is it now localhost:5000/guests?

> Just a quick note: if you named your route anything but guests, you're going to need to make sure that the action reflects the correct route otherwise this won't work.

4. So, if you hadn't noticed, our guests route is still only showing us a hardcoded list. This is because we haven't done anything with the input data yet. Let's change that.

First, you should just get rid of all of the hard-coded information in your list of guests. We won't be needing that. You'll want to still have a list, though, but we're just going to initialize it to be empty. You all know what this looks like, but just to be clear, your guest list looks like this now:

```python
guests = []
```

5. Now, let's access the information from our form. Because our 'guests' route now needs to accept a POST request, we need to make sure we add our methods list:

```python
@app.route('/guests', methods=['GET', 'POST'])
```

You'll notice that unlike my example above, I specified 'GET' and 'POST'. This is important: if the request sent to the guests route is a 'GET' request, we still want to show the user the information in that route. If the request is a POST, though, we'll need to add a new guest to the list. I'll outline this logic below in pseudocode:

```python
@app.route('/guests', methods=['GET', 'POST'])
def guests():

  if request is 'GET':
    show guest list
  else:
    add new guest to list
    then, show the updated list
```

### Important:

Flask gives us a ton of handy tools for accessing requests as well as the data that's sent with them. It actually gives us a **request** object to work with. At the top of your app.py file, I want you to go ahead and import request from flask. Your imports should look like this:

```python
from flask import Flask, render_template, request
```

We're going to use this object to access the data sent in the request from our form (client) as well as to figure out what kind of request we're dealing with.

6. Now, we need to do something with our form data (I feel like I've said this before).

#### request.args & request.form

We're going to be using the request object that Flask gives us to access the information sent in the body of the request. To do this, the request object has two different properties: args and form. The only difference between the two are what kind of request they work with. Request.args only works to obtain data from GET requests, and request.form only works to obtain the data from POST requests.

Can you guess which one we'll be using?

Hell yeah! We're going to be using "request.form" to access our data.

You can print out request.form in your route to see what the data you're sent looks like. You may see something weird called "ImmutableMultiDict" print out. We're not going to get too deep into this right now. Basically, this is a kind of dictionary that we can access two different ways:

request.form['keyname']

OR (and slightly better)

request.form.get('keyname')

The latter method is slightly better, because it doesn't make any assumptions. Using the bracket notation indicates that that key you're looking to access WILL be there, and so it will throw an error if the key doesn't happen to be there (remember how we made the plus one optional?). Using the .get() method will return "None" if the key you're looking for doesn't exist rather than throwing errors. This makes debugging so much easier.

In your guests route, you're going to want to access all of the information that we had our user give us. To do this, you're going to access the **name** attribute of the form using request.form.get('name'). This will return the value that the user input.

I'll give you the first one, see if you can do the rest:

```python
@app.route('/guests', methods=['GET', 'POST'])
def guests():
  if request.method == 'POST':
    # We need to check if the method is post, remember, so that we handle the data from the form only in that instance. If we try to access form data on a "GET" request, we'll get errors.
    guest_name = request.form.get('name')
    guest_email = # TODO: fill this in
    guest_plus_one = #TODO: fill this in
    # TODO: get the rest of the information
    return render_template('guests.html')
```

You're going to want to return the template at the end, too. But wait, we're missing something.

7. 
