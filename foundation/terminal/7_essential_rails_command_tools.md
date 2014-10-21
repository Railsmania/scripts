# Command Line Basics
There are a few commands that are absolutely critical to your everyday usage of Rails. In the order of how much you'll probably use them are:

rails console
rails server
rake
rails generate
rails dbconsole
rails new app_name
All commands can run with -h or --help to list more information.

Let's create a simple Rails application to step through each of these commands in context.

# rails new
The first thing we'll want to do is create a new Rails application by running the rails new command after installing Rails.

You can install the rails gem by typing gem install rails, if you don't have it already.

$ rails new commandsapp
     create
     create  README.rdoc
     create  Rakefile
     create  config.ru
     create  .gitignore
     create  Gemfile
     create  app
     ...
     create  tmp/cache
     ...
        run  bundle install
Rails will set you up with what seems like a huge amount of stuff for such a tiny command! You've got the entire Rails directory structure now with all the code you need to run our simple application right out of the box.

# rails server
The rails server command launches a small web server named WEBrick which comes bundled with Ruby. You'll use this any time you want to access your application through a web browser.

With no further work, rails server will run our new shiny Rails app:

$ cd commandsapp
$ bin/rails server
=> Booting WEBrick
=> Rails 4.1.4 application starting in development on http://0.0.0.0:3000
=> Call with -d to detach
=> Ctrl-C to shutdown server
[2013-08-07 02:00:01] INFO  WEBrick 1.3.1
[2013-08-07 02:00:01] INFO  ruby 2.0.0 (2013-06-27) [x86_64-darwin11.2.0]
[2013-08-07 02:00:01] INFO  WEBrick::HTTPServer#start: pid=69680 port=3000
With just three commands we whipped up a Rails server listening on port 3000. Go to your browser and open http://localhost:3000, you will see a basic Rails app running.

You can also use the alias "s" to start the server: rails s.

The server can be run on a different port using the -p option. The default development environment can be changed using -e.

$ bin/rails server -e production -p 4000
The -b option binds Rails to the specified IP, by default it is 0.0.0.0. You can run a server as a daemon by passing a -d option.

# rails generate
The rails generate command uses templates to create a whole lot of things. Running rails generate by itself gives a list of available generators:

You can also use the alias "g" to invoke the generator command: rails g.

$ bin/rails generate
Usage: rails generate GENERATOR [args] [options]
 
...
...
 
Please choose a generator below.
 
Rails:
  assets
  controller
  generator
  ...
  ...
You can install more generators through generator gems, portions of plugins you'll undoubtedly install, and you can even create your own!

Using generators will save you a large amount of time by writing boilerplate code, code that is necessary for the app to work.

Let's make our own controller with the controller generator. But what command should we use? Let's ask the generator:

All Rails console utilities have help text. As with most *nix utilities, you can try adding --help or -h to the end, for example rails server --help.

$ bin/rails generate controller
Usage: rails generate controller NAME [action action] [options]
 
...
...
 
Description:
    ...
 
    To create a controller within a module, specify the controller name as a
    path like 'parent_module/controller_name'.
 
    ...
 
Example:
    `rails generate controller CreditCard open debit credit close`
 
    Credit card controller with URLs like /credit_card/debit.
        Controller: app/controllers/credit_card_controller.rb
        Test:       test/controllers/credit_card_controller_test.rb
        Views:      app/views/credit_card/debit.html.erb [...]
        Helper:     app/helpers/credit_card_helper.rb
The controller generator is expecting parameters in the form of generate controller ControllerName action1 action2. Let's make a Greetings controller with an action of hello, which will say something nice to us.

$ bin/rails generate controller Greetings hello
     create  app/controllers/greetings_controller.rb
      route  get "greetings/hello"
     invoke  erb
     create    app/views/greetings
     create    app/views/greetings/hello.html.erb
     invoke  test_unit
     create    test/controllers/greetings_controller_test.rb
     invoke  helper
     create    app/helpers/greetings_helper.rb
     invoke    test_unit
     create      test/helpers/greetings_helper_test.rb
     invoke  assets
     invoke    coffee
     create      app/assets/javascripts/greetings.js.coffee
     invoke    scss
     create      app/assets/stylesheets/greetings.css.scss
What all did this generate? It made sure a bunch of directories were in our application, and created a controller file, a view file, a functional test file, a helper for the view, a JavaScript file and a stylesheet file.

Check out the controller and modify it a little (in app/controllers/greetings_controller.rb):

class GreetingsController < ApplicationController
  def hello
    @message = "Hello, how are you today?"
  end
end
Then the view, to display our message (in app/views/greetings/hello.html.erb):

<h1>A Greeting for You!</h1>
<p><%= @message %></p>
Fire up your server using rails server.

$ bin/rails server
=> Booting WEBrick...
The URL will be http://localhost:3000/greetings/hello.

With a normal, plain-old Rails application, your URLs will generally follow the pattern of http://(host)/(controller)/(action), and a URL like http://(host)/(controller) will hit the index action of that controller.

Rails comes with a generator for data models too.

$ bin/rails generate model
Usage:
  rails generate model NAME [field[:type][:index] field[:type][:index]] [options]
 
...
 
Active Record options:
      [--migration]            # Indicates when to generate migration
                               # Default: true
 
...
 
Description:
    Create rails files for model generator.
For a list of available field types, refer to the API documentation for the column method for the TableDefinition class.

But instead of generating a model directly (which we'll be doing later), let's set up a scaffold. A scaffold in Rails is a full set of model, database migration for that model, controller to manipulate it, views to view and manipulate the data, and a test suite for each of the above.

We will set up a simple resource called "HighScore" that will keep track of our highest score on video games we play.

$ bin/rails generate scaffold HighScore game:string score:integer
    invoke  active_record
    create    db/migrate/20130717151933_create_high_scores.rb
    create    app/models/high_score.rb
    invoke    test_unit
    create      test/models/high_score_test.rb
    create      test/fixtures/high_scores.yml
    invoke  resource_route
     route    resources :high_scores
    invoke  scaffold_controller
    create    app/controllers/high_scores_controller.rb
    invoke    erb
    create      app/views/high_scores
    create      app/views/high_scores/index.html.erb
    create      app/views/high_scores/edit.html.erb
    create      app/views/high_scores/show.html.erb
    create      app/views/high_scores/new.html.erb
    create      app/views/high_scores/_form.html.erb
    invoke    test_unit
    create      test/controllers/high_scores_controller_test.rb
    invoke    helper
    create      app/helpers/high_scores_helper.rb
    invoke      test_unit
    create        test/helpers/high_scores_helper_test.rb
    invoke    jbuilder
    create      app/views/high_scores/index.json.jbuilder
    create      app/views/high_scores/show.json.jbuilder
    invoke  assets
    invoke    coffee
    create      app/assets/javascripts/high_scores.js.coffee
    invoke    scss
    create      app/assets/stylesheets/high_scores.css.scss
    invoke  scss
   identical    app/assets/stylesheets/scaffolds.css.scss
The generator checks that there exist the directories for models, controllers, helpers, layouts, functional and unit tests, stylesheets, creates the views, controller, model and database migration for HighScore (creating the high_scores table and fields), takes care of the route for the resource, and new tests for everything.

The migration requires that we migrate, that is, run some Ruby code (living in that 20130717151933_create_high_scores.rb) to modify the schema of our database. Which database? The sqlite3 database that Rails will create for you when we run the rake db:migrate command. We'll talk more about Rake in-depth in a little while.

$ bin/rake db:migrate
==  CreateHighScores: migrating ===============================================
-- create_table(:high_scores)
   -> 0.0017s
==  CreateHighScores: migrated (0.0019s) ======================================
Let's talk about unit tests. Unit tests are code that tests and makes assertions about code. In unit testing, we take a little part of code, say a method of a model, and test its inputs and outputs. Unit tests are your friend. The sooner you make peace with the fact that your quality of life will drastically increase when you unit test your code, the better. Seriously. We'll make one in a moment.

Let's see the interface Rails created for us.

$ bin/rails server
Go to your browser and open http://localhost:3000/high_scores, now we can create new high scores (55,160 on Space Invaders!)

# rails console
The console command lets you interact with your Rails application from the command line. On the underside, rails console uses IRB, so if you've ever used it, you'll be right at home. This is useful for testing out quick ideas with code and changing data server-side without touching the website.

You can also use the alias "c" to invoke the console: rails c.

You can specify the environment in which the console command should operate.

$ bin/rails console staging
If you wish to test out some code without changing any data, you can do that by invoking rails console --sandbox.

$ bin/rails console --sandbox
Loading development environment in sandbox (Rails 4.1.4)
Any modifications you make will be rolled back on exit
irb(main):001:0>

# rails dbconsole
rails dbconsole figures out which database you're using and drops you into whichever command line interface you would use with it (and figures out the command line parameters to give to it, too!). It supports MySQL, PostgreSQL, SQLite and SQLite3.

You can also use the alias "db" to invoke the dbconsole: rails db.

# rails runner
runner runs Ruby code in the context of Rails non-interactively. For instance:

$ bin/rails runner "Model.long_running_method"
You can also use the alias "r" to invoke the runner: rails r.

You can specify the environment in which the runner command should operate using the -e switch.

$ bin/rails runner -e staging "Model.long_running_method"

# rails destroy
Think of destroy as the opposite of generate. It'll figure out what generate did, and undo it.

You can also use the alias "d" to invoke the destroy command: rails d.

$ bin/rails generate model Oops
      invoke  active_record
      create    db/migrate/20120528062523_create_oops.rb
      create    app/models/oops.rb
      invoke    test_unit
      create      test/models/oops_test.rb
      create      test/fixtures/oops.yml
$ bin/rails destroy model Oops
      invoke  active_record
      remove    db/migrate/20120528062523_create_oops.rb
      remove    app/models/oops.rb
      invoke    test_unit
      remove      test/models/oops_test.rb
      remove      test/fixtures/oops.yml
