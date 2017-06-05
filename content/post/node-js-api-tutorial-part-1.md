+++
date = "2016-06-11"
title = "Node.js API Tutorial Part 1"
tags = ["javascript", "node", "tutorial"]
series = ["Node API Tutorial"]
+++

[Node.js](https://nodejs.org) is a great technology for creating web apps!
In this first tutorial we are going to lay the foundation for a simple website
powered by a Node api server. By the end of this series of tutorials, we will
have a Node website with basic user registration and authentication capabilities.
I assume that you have a basic understanding of JavaScript and web development.
You can find the full source code for the project [here](https://github.com/Traviskn/node_api_tutorial).
Let's dive in!

## Installing Our Tools

First you will need to install [node.js](https://nodejs.org).  You can install
it directly from the [official website](https://nodejs.org/en/download/) on either
Mac, Linux, or Windows.  I am writing this tutorial on a Mac running Node version
6.2.0.  While there will be some differences depending on your operating system
of choice, as long as you are on the same version of Node things should work.

Open up your command line and test out the following commands:
```shell
$ node -v
$ npm -v
```
If you see a version number printed out to the screen for both of those commands,
then you are ready to go!

Note that the leading '$' indicates a shell prompt, and not a character that you
need to type in.  The commands to run are on each line following the '$', so be
careful not to copy and paste the '$' sign.

## Creating Our Project

Create a folder to hold our project called sample_app, and initialize it as a
Node project using the NPM tool:

```shell
$ mkdir sample_app
$ cd sample_app
$ npm init
```

You will be prompted for some information about the project such as author name.
Go ahead and accept the defaults by just hitting enter if you aren't sure what
to put.  Afterwards you should see a new package.json file in your project folder.

My favorite library for creating Node.js servers is [Hapi](http://hapijs.com/).
Hapi is famous for supporting Walmart's e-commerce applications under massive
load during black Friday.  Install Hapi using the Node Package Manager, or NPM.

```shell
$ npm install hapi --save
```

If you look at the package.json file, you will see that npm added Hapi as a
dependency.  That's what the 'save' option does. This will help you keep track
of all the libraries needed to run your project!  You will also notice a new
folder called 'node_modules'.  This folder is where npm downloads libraries for
you to use.

## Your First Node.js Server

Let's start writing our first piece of node.js code!  Create a file called index.js
with the following content:

```javascript
'use strict';

const Hapi = require('hapi');

// Create a server with a host and port
const server = new Hapi.Server();
server.connection({
    host: 'localhost',
    port: 8000
});

// Add the home route
server.route({
    method: 'GET',
    path:'/',
    handler: function (request, reply) {
        return reply('Hello, Node.js!');
    }
});

// Start the server
server.start((err) => {
    if (err) {
        throw err;
    }
    console.log('Server running at:', server.info.uri);
});
```

Depending on your level of JavaScript experience, some of the syntax in this file
might look a little strange to you.  Keywords like 'const' and arrow functions
written with '() => {}' are new additions to the most recent official language
standard, ES6 (or ES2015).  While ES6 is not fully supported in every browser,
most of it's features *are* supported in the newest versions of Node.

Start your server with the following command:
```shell
node index.js
```
You should see a message printed to the screen that says 'Server running at:
localhost:8000'.  Visit localhost:8000 in your web browser to see the message
'Hello, Node.js!'.  Congratulations, you've written your first Node.js web app!
You can stop the server by hitting control + c on your keyboard.

## Building a Better Project Structure

A realistic project will have more than just one file.  Let's create some better
structure for our application.  Add two folders, one called 'client' and one called
'server'.  Move the index.js file we made earlier into the 'server' folder.  Create
an empty file called index.html in the client folder.  Finally, create an empty file
called index.js in the sample_app folder.  After all is done, your project's file
structure should look like this:

```shell
sample_app/
├── index.js
├── package.json
├── client/
│   └── index.html
├── node_modules/
│   └── ...
└── server/
    └── index.js
```

open up sample_app/index.js and add the following code:

```javascript
require('./server');
```

When you require a folder like this, Node's module system will automatically look
for a file called index.js within that folder.  All this line does is import and
execute the contents of our server/index.js file.  This will give us a convenient
way to start our server!

If you open up your package.json file, you will see a section called 'scripts'.
This is a convenient place to put commands that you run often.  Add a 'start'
script like the code below:

```javascript
{
  "name": "sample_app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Travis Nuttall",
  "license": "ISC"
}
```

Now you can use npm to start your Node server.  Open your command line in the
'sample_app' folder and run the following command:

```shell
$ npm run start
```

Just like before, you should see a message printed to the screen that says
'Server running at: localhost:8000'. Visit localhost:8000 in your web browser
to be sure you still see the message 'Hello, Node.js!'.  Remember that the code
in server/index.js should match the code we wrote in the previous section, 'Your
First Node Server'.

Remember that you must be in the same directory as the package.json file in order
to run npm commands, and that ours is in the project root; the sample_app
directory.

## Adding a Homepage

Node can serve static HTML files just like any web server.  Hapi uses a plugin
called Inert to enable this.  Install it with NPM:

```shell
$ npm install inert --save
```

Now we can update our code in server/index.js to serve the index.html file for
our home route.

```javascript
'use strict';

const Hapi = require('hapi');
const Inert = require('inert');
const path = require('path');

// Create a server with a host and port
const server = new Hapi.Server({
   // Configure where static files are located
    connections: {
        routes: {
            files: {
                relativeTo: path.join(__dirname, '../client')
            }
        }
    }
});

server.connection({
    host: 'localhost',
    port: 8000
});

// Register plugins
server.register([Inert], (err) => {
    if(err) {
        throw err;
    }

    // Add the home route
    server.route({
        method: 'GET',
        path:'/',
        handler: {
            file: './index.html'
        }
    });

    // Start the server
    server.start((err) => {
        if (err) {
            throw err;
        }
        console.log('Server running at:', server.info.uri);
    });
});
```

Let's fill out our homepage now by editing client/index.html.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Sample App</title>
  </head>
  <body>
    <h1>Sample App Homepage</h1>
  </body>
</html>
```

Start you server again by running 'npm run start' in the 'sample_app' directory.
If you visit localhost:8000 you should see the heading 'Sample App Homepage'.  
Your server can now handle HTML files!

It is worth noting that Hapi can also do more advanced HTML templating with a package
called [Vision](https://github.com/hapijs/vision). You could create dynamic HTML
pages on your server just like other web programming tools such as PHP or Ruby on
Rails.  In this guide however we are going to focus on creating an API and will
not cover any server-side templating.

## A Demo API

API, or Application Programming Interface, can mean many different things depending
on the context.  In these tutorials our API is a web service that can handle creating,
reading, updating, and deleting data.  Rather than rendering complete web pages
with web forms to accomplish this, our API will simply send and receive raw data
leaving the display and formatting of this data up to the client app.  A 'client
app' could be HTML and JavaScript running in a browser, or it could even be an
Android or iPhone application.  This is one of the major reasons that API's have
become popular recently - they are flexible enough to support both web and mobile
apps.

Let's create a very simple demo of how our API and web app are going to work.
This will just be a quick and easy demo which will be extended in future tutorials
in this series.

Open up server/index.js and update it to match the code below.  The key differences
are in the server.route method about halfway down the file.

```javascript
'use strict';

const Hapi = require('hapi');
const Inert = require('inert');
const path = require('path');

// Create a server
const server = new Hapi.Server({
   // Configure where static files are located
    connections: {
        routes: {
            files: {
                relativeTo: path.join(__dirname, '../client')
            }
        }
    }
});

// Configure the server host and port
server.connection({
    host: 'localhost',
    port: 8000
});

// Register plugins
server.register([Inert], (err) => {
    if(err) {
        throw err;
    }

    // Add the home route and api messages route
    server.route([
      {
        method: 'GET',
        path:'/',
        handler: {
            file: './index.html'
        }
      }, {
        method: 'GET',
        path: '/api/messages/',
        handler: function(request, reply) {
          // Create some dummy data to send back
          return reply({
            data: [
              { id: 1, message: "Hello there!" },
              { id: 2, message: "What's up?" },
              { id: 3, message: "How are you?"}
            ]
          });
        }
      }
    ]);

    // Start the server
    server.start((err) => {
        if (err) {
            throw err;
        }
        console.log('Server running at:', server.info.uri);
    });
});
```

Restart your server and open `localhost:8000/api/messages/` in your browser.  You
should see some raw data in a format called JSON, or JavaScript Object Notation.
We've structured our data under a key called, appropriately enough, data.  The data
is just a list of 3 messages, complete with an ID and the actual text of the message.
While this data is obviously not very nice for a human to read, it is perfect for
a computer program!

Let's open up client/index.html and add a simple client-side application to read
and format this data a little better for a human user.  Update your file to match
the code below:

```html
<!DOCTYPE html>

<html lang="en">
  <head>
    <meta charset="UTF-8">

    <title>Sample Message App</title>
  </head>

  <body>
    <h1>Sample Message App</h1>

    <button id="getMessagesButton">Get Messages</button>
    <button id="clearMessagesButton">Clear Messages</button>

    <ul id="messageList"></ul>

    <script src="https://code.jquery.com/jquery-3.0.0.min.js"></script>
    <script type="text/javascript">
        'use strict'

        // Wait until the page is ready for our code to run
        jQuery(document).ready(function() {
            // Get messages when the button is clicked
            var getMessagesButton = $('#getMessagesButton');
            getMessagesButton.click(function() {
                getMessages();
            });

            // Clear messages when the button is clicked
            var clearMessagesButton = $('#clearMessagesButton');
            clearMessagesButton.click(function() {
                clearMessages();
            });

            // Hide the clear messages button at first
            clearMessagesButton.hide();

            // Get the list that will hold messages
            var messageList = $('#messageList');

            // Get the message data from our API
            function getMessages() {
                $.get('/api/messages/', function(response) {
                    renderMessages(response.data);
                });
            }

            // Display the message data in a nice list format
            function renderMessages(data) {
                var messages = data.map(function(m) {
                    return $('<li id="' + m.id + '">' + m.message + '</li>');
                });

                messageList.html(messages);

                getMessagesButton.hide();
                clearMessagesButton.show();
            }

            // Remove all the messages from the list
            function clearMessages() {
                messageList.empty();

                clearMessagesButton.hide();
                getMessagesButton.show();
            }
        });
    </script>
  </body>
</html>
```

There's quite a bit of code here, so let's go through it step by step.  First in
the HTML we added two buttons, one to get the message data from our API, and another
to clear the messages out once we are done with them.  Next we added an empty list
to hold the messages we get from the API.

Near the bottom of the HTML page we added some 'script' tags to hold our simple
JavaScript application.  I decided to link jQuery into our page to help make our
code a bit easier to write.  jQuery is one of the most popular libraries in use
on the web today, so hopefully you have seen it before.

I put all our code in a document ready callback which will wait until the page is
loaded before running our code.  I grab our two buttons by their ID's and set up
click handlers to get and clear messages respectively.  I also grab our message
list by it's ID so that we can add our messages to it once we get them. Finally
I define 3 functions; one to get message data from the API, another to format the
message data into an HTML list and add it to the page, and finally one to clear
out the messages once we are done with them.

Restart your server and go to localhost:8000 in your browser to see the simple messages
app.  You can press 'Get Messages' to populate the list with messages from the API,
and then you can press 'Clear Messages' to get rid of them.  Right click the page
and select 'Inspect Element' to see how the HTML of the page is changing.  Click
the 'Network' tab of the inspector to see the API request and response made when
you click 'Get Messages'.  Notice how the page does not need to refresh in order
to get the messages!  This is how many popular email applications work, which are
able to create, read, and delete emails without ever refreshing the page.  All the
data is being managed by JavaScript running in your browser.  The application feels
much faster and more responsive when you can avoid refreshing the page.

You can perhaps imagine how you might extend this small application.  Right now
all we have are three hardcoded messages, but if we add a database we could easily
manage many more messages.  We could add buttons to create new messages and send
them to our API server to be saved to a database.  Since each message is rendered
as a list item with an ID, it would be possible to add a 'Delete' button to each
message and send a delete request to your API server to tell it to delete the message
from a database. You could potentially add all sorts of messaging functionality
if you wanted! Unfortunately this is where our demo app ends.  We will add more
functionality to it in upcoming tutorials.

## Next Steps

We have created a very basic Node app with just a homepage.  Next we need to give
our app the ability to work with a database.  Stay tuned for part 2 where we will
cover using a relational SQL database to store and retrieve data!
