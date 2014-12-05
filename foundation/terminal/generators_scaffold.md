Lesson on generators

generators generate code that is necessary for the app to work.

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


You can install more generators through generator gems, portions of plugins you'll undoubtedly install, and you can even create your own!


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
