# SQL & SQLAlchemy

Resources serve as a guide to orient your direction.
You do not need to read or watch all the resources.
They are simply here to serve you.

## Demo & Source Code

[Demo Video](https://drive.google.com/file/d/1ukrInX_GAQkoNEyhPM9y-Rgo9aUFGoDT/view?usp=sharing)

*Please try to complete the assignment and troubleshoot on your own before checking out the finished code. When you're done, this is a great way to see if we solved things the same way, but don't use this as a resource to copy, you won't learn as much as solving problems and looking things up on your own when you get stuck.*

[Finished Source Code](https://github.com/sidneyarcidiacono/event-app)

Here's an [additional demo video](https://drive.google.com/file/d/1WFSjq_at4QgNKU_spZgAlTSFRO3Y5bEG/view?usp=sharing) from a different assignment that may also be useful if you want some assistance understanding `__repr__()` methods and visualizing your data.

## SQLAlchemy

[Official SQLAlchemy Docs](https://www.sqlalchemy.org)

[Flask SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/)

Here is a good video by Pretty Printed on connecting to a SQLite database using SQLAlchemy. One thing I want to point out is that this video is quite old. SQLite3 is a C library and is built into Python and we didn't need to install it separately like he does. If you run into issues with connecting to SQLite out of the box, send me a message and we can troubleshoot.

If you want to skip his installation, I recommend jumping to [here](https://youtu.be/KrRzZGcHjK8?t=162)

[Connecting to SQLite Database with SQLAlchemy](https://www.youtube.com/watch?v=KrRzZGcHjK8)

[Getting Started with Flask-SQLAlchemy](https://www.youtube.com/watch?v=jTiyt6W1Qpo)

## SQL vs. NoSQL databases

[This](https://towardsdatascience.com/databases-101-sql-vs-nosql-which-fits-your-data-better-45e744981351) is a great post that goes over quite a bit of the terminology you've heard throughout this assignment and gives a great diagram to understand the layout of SQL databases.

I want to point out that he mentions that NoSQL databases use Object Relational Mapping and I just want to clarify that what this means is that NoSQL databases use Object Relational Mapping *out of the box* as a *key feature or attribute*. SQLAlchemy creates this abstraction for us, whereas some NoSQL database management systems represent documents similarly to objects *without this extra step*.

You'll be learning about MongoDB if you're in Web1.1 this term, and will have a much better idea of the differences between the two types of database systems both in concept and practice.

If you aren't in Web1.1 or want to learn about MongoDB ahead of time, **Slack me** and I'll look into getting a short Mongo tutorial out if enough folks are interested!
