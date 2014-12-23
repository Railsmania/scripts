[Jordi]
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

Let's look at a real Rails project. In particular it is a book sharing website.

Rails autogenerates a project structure. You can know more about this structure in our video about the files on your project.
Let's open the app folder where much of the code of our project lives. Here we can see directories for controllers, models, views and two extra components, assets and helpers, let's not bother about these two in this lesson.

When we open the app/controllers directory we see three controllers, application_controller, users_controller.rb and books_controller.rb. Every Rails application has an application_controller which serves as a base for all our controllers. We'll talk in deep about this in a lesson about controllers.
The rest of our controllers are always named with a name in plural + the word 'controller' and as they are ruby files they finish with '.rb'. This is a very important convention in Rails. Let's understand why looking at the views directory.

Under views we have a layouts subdirectory which we will ignore for now and a subdirectory named after each of the controllers.
Inside any of these directories we have several template files, in this case we are using the .erb template language so their extension is .erb. These files names also follow a convention. If we go back to our books controller and we open it, we can see these 'def index, def show' lines which are ruby methods. We see we have one view file named after each of these methods. Using this convention Rails controllers can automatically find and render a view without us writing any code.

Finally let's look at the models subdirectory. We have a user.rb and book.rb files. This is again a rails convention. Models names are in singular. When we open the book.rb file we see it is practically empty. we are just defining the Photo class to inherit from activerecord::Base. This is enough for Rails to create many methods for us. In particular, we will have many methods to update and retrieve data from a database table called books, again a Rails naming convention.


As an extra component, not part of MVC but very important in Rails, let's look at our config/routes.rb file. We can see this line:
resources :books.

This line will connect specific URLs with our controllers following again Rails conventions. We explain in deep about this important file in the routes course lessons, please take a look if you want to know more details about what is happening here.

With Routing we can expand our diagram, User -> Routing -> MVC

With all these components in place, let's look how they work together to bring an HTML to the browser of the user.

First we write the URL
localhost:3000/books

The browser will issue a request "GET /books" to our server.
We hit the routing which was instructed to understand about 'books', on this URL in particular by convention it goes to the books_controller, to the method '#index'.

Let's look at the method. We see this code:
@books = Book.all

We are using our Photo model to retrieve all the books in our database. We call this data @books.  Our method finishes here, by default, Rails will render the view for this controller with the name of the method.
We navigate to views/books/index.html.erb, open this file and we see:
<%= @books.each do |book| %>
  <div> <%= book.title %> </div>
  <div> <%= book.author %> </div>
<%= end %>

A bit of code to generate HTML tags to show all our books. This gets rendered and in our browser and we see a collection of books!

These are the major components of Rails and its main conventions. By follow them we can create websites very easily and with very few lines of code which we can even autogenerate!
From this lesson you can learn more about routing on the routing lessons, about installing extra software in the gemfile lessons or jump straight into the hello world lesson.






