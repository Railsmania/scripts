# Basic concepts and Hello world in Rails.

In this lesson we'll review the basics of working on and in a Ruby on Rails project.

# New project
Let’s get started by creating a new project. Creating a new project is very easy, just execute the "rails new" command in the terminal:

rails (space) new (space) myproject.

This will create a directory with a working project called myproject.
This project already contains everything you need to run your web
application.
Let’s take a look at its contents.
At the highest level we can see several files and directories. In this
video we will explain only the most important files.

# Gemfile
The first file we want to focus on is the Gemfile.

In the Gemfile we define all the software our application depends upon.

###(open Gemfile) 
Ruby software usually is packed in Gems. The Gemfile is
a list of all the gems our Rails project uses.
We can see for instance that we even define the Rails version our
project depends upon.
We can see there are many other gems in this file. Most of the power of
Rails comes from installing useful gems which provide functionality to
your application. We will teach you how to find, install and use gems in
your projects. 

#####(close Gemfile)

# Bundler
When we modify the Gemfile , we must execute the command 
‘bundle install’ (execute) 

this will download and install all the software
defined in the Gemfile and will be available in our project.

# Webrick
Even though we just generated our project, we can already launch a web server with our application. Make sure you are in your project's directory. Then, in the terminal,  run:

bundle execute rails server

This command starts a web server that will serve the default
rails page when accesed with a browser. The default landing page is not very interesting but we know our project is working properly when we see it.  

##### (Close browser)

# Config/routes
The next important directory is config/  All the configuration files for your
project are contained here. For now we can safely use the defaults. We'll explain each file in future lessons as we need to modify them.

But one file that you will most certainly modify in all your projects is the config/routes.rb

This file by default is full of examples of its usage and defines
the routes of your project. Routes are used to map the url that the
user types in the browser to the software code in your project's controllers.

As an example, let’s uncomment this line for the root route.

Root means the url when the user goes to your site without indicating
any path. The ‘welcome#index’ format indicates the welcome controller,
index action. Meaning, when the user comes to the root url of
your site the index action of the welcome controller will be called.

# Controllers and Actions
So what is an action? What is a controller?

Well, back to the filesystem we can see an app directory.  Most of what is
identified with a Rails application is inside this directory.
Rails follows an architecture called MVC for Model-View-Controller. We
can see that we have subdirectories for models, views and controllers
under app.

So when a user visits our application on any given url, Rails
will first look at the config/routes.rb file to determine which controller will handle the request for the user.

We configured config/routes.rb to use the welcome controller , let’s
create that file (create proper file).

All the controllers inherit from Application controller, this is the
line to create a new controller named welcome:

class WelcomeController < ApplicationController
end


In the config/routes.rb we wrote ‘welcome#index’ meaning the index
action inside the welcome controller.

To create a new action, we just create a new Ruby method:

```
class WelcomeController < ApplicationController
def index
end
end
```

Now the controller is ready, this method will be called but at this point it won't return anything useful. We still need to have a view to send to the
user’s browser.

# Views
We have an app/views directory where all the views live. By convention a
controller renders views from a directory with the same name as the controller. So let’s
create a welcome directory.

Now we need to create an index view. Just create a index.html.erb
file. The erb extension indicates this file will be executed by rails
before becoming html, this allows us to create dynamic content. For instance we can do this mix of ruby and html:
<h1> Hello World</h1>
<%= 'Hello from Ruby' %>

Let’s restart our rails server by going to the terminal and executing the 
bundle exec rails server
command

Now we can reopen a browser tab to our project tab and there we can see our first Hello World webpage through Ruby on Rails!


That’s all for this lesson, we encourage you to explore the other files
in this new project. Since generating new projects is very easy, if you break anything, just delete the project and create a new one!

Happy coding!