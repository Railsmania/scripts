# Basic concepts and Hello world in Rails.

On this lesson we are gonna show you the structure of a Rails project.

Let’s get started by creating a new project. This is very easy, just
execute the command in the terminal:
rails (space) new (space) myproject.

This will create a directory with a working project called myproject.
This project already contains everything you need to run your web
application.
Let’s take a look at its contents.
At the highest level we can see several files and directories. In this
video we are gonna explain only the most important files.

The first file we want to focus on is the Gemfile.

In the Gemfile we define all the software our application depends on.
(open the file) Ruby software usually is packed in Gems. The Gemfile is
a list of all the gems our Rails project uses.
We can see for instance that we even define the Rails version our
project depends on.
We can see there are many other gems in this file. Most of the power of
Rails comes from installing useful gems which provide functionality to
your application. We will teach you how to find, install and use gems in
your projects. (close the file)

When we modify the Gemfile , we must execute the command ‘bundle
install’ (execute) this will download and install all the software
defined in the Gemfile and will be available in our project.

At this point we can already launch a web server with our application
running. From the directory of our project let’s execute:
rails server

This command will start a web server and will start serving the default
rails page when accesed with a browser.
As we can see, the stock page is not very interesting but we know our
project is working properly.  Let’s get back to the filesystem.

The next important directory is config/  All the configuration of your
project is contained here. At this point we already have a default
configuration that just works, we will explain each file in future
lessons when we need to modify it.

One file that you will modify in any project is the config/routes.rb
file. This file by default is full of examples of its usage and defines
the routes of your project. Routes are used to map from the url that the
user types in the browser and the software in your project which will be
executed.

As an example let’s uncomment this line
root means the url when the user goes to your site without indicating
any path. The ‘wellcome#index’ format. Indicates the welcome controller,
index action. Meaning, when the user comes to the root url, no path, of
your site the index action of the welcome controller will be called.
So the next question is , what is an action? What is a controller?

Back to the filesystem we can see an app directory.  Most of what is
identified with a Rails application is inside this directory.
Rails follows an architecture called MVC for Model-View-Controller. We
can see that we have subdirectories for models, views and controllers
under app.
As we said, when the user visits our application on a given url, Rails
will first look at the config/routes.rb file and then based on that file
will  decide which controller will handle the request from the user.
Typically the controller will need to gather some data to show to the
user, that data is managed by the models and the data is displayed
inside some HTML, this is the view.
In this lesson we do not have any data so we will only present the
controllers and the views directories.

We configured config/routes.rb to use the welcome controller , let’s
create that file (create proper file).

All the controllers inherit from Application controller, this is the
line to create a new controller named welcome:

class WelcomeController < ApplicationController
end


in the config/routes.rb we wrote ‘welcome#index’ meaning the index
action inside the welcome controller.
To create a new action, we just create a new Ruby method. This is how is
done:

class WelcomeController < ApplicationController
def index
end
end


Now the controller is ready, this method will be called but we don’t
have anything to give back. We still need to have a view to send to the
user’s browser.
We have a app/views directory where all the views live. By convention a
controller render views from a directory with its same name. So let’s
create a welcome directory.
Now we are gonna create an index view. Just create a index.html.erb
file. The erb extension indicates this file will be executed by rails
before becoming html, this allows us to create dynamic content. For this
lesson we will stick to static HTML:
<h1> Hello World </h1>

Let’s restart our rails server by going to the terminal and pressing
ctl+c or command+c on Mac. Now we execute rails server again and we go
to the our project tab and there we can see our first webpage through
Ruby on Rails !


That’s all for this lesson, we encourage you to explore the other files
in a new project. If you break something , delete the project and create
a new one, it is easy!
We will explore other files and directories as we advance in our
lessons.
