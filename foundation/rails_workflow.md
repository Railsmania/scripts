# Rails basic workflow

In this lesson we are gonna explain the basic concepts of RubyonRails. Please watch this lesson before any other lessons if you are absolutely new to RubyonRails.

RubyOnRails is built using the Ruby language. You do not need to learn to program using Ruby in general right now though. Most of the code you will be creating on the first few lessons is limited by Rails conventions, we recommend to watch our videos and understand how to make simple things with Rails before going deeper into the Ruby language.

RubyOnRails is used to program server software to create websites or to power web APIs.

RubyOnRails follows a pattern called MVC for Model-View-Controller. This pattern encourages the separation of concerns which usually makes your application easier to write and easier to maintain.
Let's talk about the three parts of the MVC pattern.

The first part, the model has the business logic of your application and its data. Most of the code of your application would be written here. Models abstract data and operations on that data. For instance, if you were building Facebook, you would have a model for user, a model for post, etc. A user would be able post information and a post could be liked, etc. But models never have an UI.

The second part, the View is the UI the user interacts with. In the case of websites, we ultimately want HTML. Views in RubyonRails are templates which after being processed will generate HTML. Views use models to fill the templates but should not understand anything about the business logic. For instance a view may use the name of the user to write it on top-right of the HTML page but does not really know where the name comes from (maybe straight from a database, maybe preprocessed, etc.)

The third component is the Controller, the controller takes care of the request. In the case of RubyOnRails, this is a HTTP request. In general the browser will either make a GET request for information from the server or POST some information to the server. In the case of making a GET request to the server, the controller will retrieve data using a model and pass it to the view. The view will render with that data. Finally the controller will pass the rendered HTML back to the user's browser.
For instance if you try to see a profile, the controller will use the User model to find the information of the user, and pass it to the user's profile template which will render the HTML with the profile of that user.

This is the MVC pattern used by RubyOnRails and many other web frameworks. This pattern is also common outside web programming so it is very useful to know it well.


In rails there is one more component worth mentioning here.
Rails has a routing component, it gets the URL of the user and finds the controller which will serve it.


Let's go through a normal scenario:

User writes a URL in the browser.
The browser sends the URL and other stuff we'll talk about in future lessons to the server. This is called a request.
The server checks the routing, decides what controller to use.
The controller calls the model and retrieve some data.
The controller pass the data to the view.
The view renders with its data modifying the html generated.
The controller sends the rendered beautiful HTML to the user.

All these processes happen on your server and RubyonRails is a framework which makes it very easy to create applications that behave like this which are a majority.



Finish here and go to hello world
or
explain stuff belown just to show all the naming conventions?






First we are gonna create a new RubyonRails application just to look at how the theory looks with a real application.
Execute this in the terminal:
```
rails new myfirstproject
```



Let's say we write this url in our browser:
http://www.example.com/books

We own www.example.com and has a RubyOnRails server running there.
So our server would see this request coming:

GET /books

Our goal is to return a list of all the books we have in the server. Let's see how we do that.



The name of the project does not matter. With this command, Rails will generate a fully functionning application for us to play with.


We need a way to find what code is gonna do this when we get this request.
We will call this the router, the part of Rails which gets an URL and returns where our code taking care of that URL lives.
We have several lessons where we explain in detail about how to use this part of Rails.
For now in a configuration file we'll write books is a resource we want to route:

resources :books


The code that will handle the request lives in a part of Rails called controller. Controllers take care of browser requests. They gather the response of our server and send it to the browser.
Each controller is divided in different code pieces which are called actions.

In Rails, we would name the controller with the name of the resource we want to work with. In this case, books, so we create a books controller. Also by convention when we want to get a list of things as in this case, the action name is 'index'.


Each action in the controller has a lot of work to do.
In the simplest case, it only needs to send an HTML page to the user's browser.
This HTML in Rails is generated by templates. These templates are called 'views'.
The controller render the views, which means any code inside the templates are executed and HTML is generated. Then the result is returned.

In Rails, controllers automatically render any view inside a directory with the controller name and with the action as its name.
In this case, we can see the content of the index view and when we visit our URL, yes, we can see the output!

This is how it is working right now:
The books url gets translated to the controller's index action which by default finds the books/index.html.erb which gets rendered and the html gets back to the user.

We got an URL working with almost no code!

Now, usually we would like to be more intelligent and insert data in our HTML dinamically. For instance, here we would like to show all our books in the html.

To do this, the controller uses a component called the model. Most of the code in a typical Rails application belongs to the models where you would write the logic of your application.

Rails by default already gives you many useful method in these models. Our Book model already have by default a method called .all which returns all books in our database.

The @ mark before the variable name in the controller means this variable will be also be accessible from the view where we can use it to render all the books information to the user.

Models are the last component of what is called the MVC architecture. M: Models, C: Controllers, V: Views.

Controllers gets a request, call a model to get some data, pass the data to a view and renders it to get a dynamic HTML and returns it to the user.


I hope you got an idea of how little you need to write to create a Rails application. And you got the idea of the general workflow of a request. In the following lessons we will teach you how to use each of these components to create all kind of applications easily.





