[Charles]
# Generators in Rails

In this lesson we are going to show how to work with a couple of generators in Rails.
Generators generate code that is necessary for the app to work and which usually follows a common pattern and can be generated automatically.

We can see all the generators available by executing the Rails generate command:
```
bundle exec rails generate
```

Let's try one of these generators. We are going to generate our own controller. But what are the arguments for this command? Let's ask the generator:
```
bundle exec rails generate controller
```

This command will give us the list of options we can use. The controller generator is expecting the parameters ControllerName action1 action2. Let's make a Greetings controller with an action of hello, which will say something nice to us.
```
bundle exec rails generate controller Greetings hello
```

What all did this generate? It made sure some directories were created, then it created a controller file, a view file, a functional test file, a helper for the view, a JavaScript file and a stylesheet file.

Let's check out the controller file. There are several methods already here. They provide all the actions needed to act upon a resource. In this case our resource is a greeting.
Let's modify the controller we have just generated a little:
```
class GreetingsController < ApplicationController
  def hello
    @message = "Hello, how are you today?"
  end
end
```
And add a route for this new action in config/routes.rb:
```
resources :greetings do
  member do
    get 'hello'
  end
end
```
Then the view, to display our message (in app/views/greetings/hello.html.erb):
```
<h1>A Greeting for You!</h1>
<p><%= @message %></p>
```
Fire up your server using rails server.
```
bundle exec rails server
```
And visit the path '/greetings/hello' in your url.

With a normal Rails application, your URLs will generally follow the pattern of http://(host)/(controller)/(action), and a URL like http://(host)/(controller) will hit the index action of that controller.

The controller generator is great when we do not have data in the DB connected to this controller, for instance in this case, we just greet without considering any data from the DB.

But if we also want to generate models and tables, we need to use another generator: scaffold.
A scaffold in Rails is a full set of model, database migration for that model, controller to manipulate it, views to view and manipulate the data, and a test suite for each of the above.

We will set up a simple resource called "HighScore" that will keep track of our highest score on video games we play.
```
bundle exec rails generate scaffold HighScore game:string score:integer
```

The generator checks that there exist the directories for models, controllers, helpers, layouts, functional and unit tests.
It creates the views, controller, model and database migrations for HighScore (creating the high_scores table and fields), takes care of the route for the resource, and create some new tests for everything.

Because we have a new migration, we need to run our migrations, we can do that with the command:
```
bundle exec rake db:migrate
```
This command will run some Ruby code (living in the 20130717151933_create_high_scores.rb file) to modify the schema of our database. After running this command we ensure our database has all the tables needed by our code.

Let's see the interface Rails created for us.
```
bundle exec rails server
```

Go to your browser and open the server url with the path /high_scores. All this html and functionality you are seeing was automatically generated. Now we can create new high scores (55,160 on Space Invaders!)

We have only used two generators in this lessons but rails has other generators. Execute:
```
bundle exec rails generate
```
To get a list of all the generators in your project. Also, aside from the standard set provide by rails, other gems can install their own generators in your project.


# rails destroy
Finally, fust a short note about another rails command: 'rails destroy'
Think of destroy as the opposite of generate. It'll figure out what generate did, and undo it.
For instance we generate a new controller:
```
bundle exec rails generate controller Oops
```

But we regret the name we chose for this controller, so we can destroy all the generated files with:
```
bundle exec rails destroy controller Oops
```

In this lesson we have seen a couple of examples of auto-generation of code with Rails. Rails has other generation commands, please experiment with them and ask us any questions!
