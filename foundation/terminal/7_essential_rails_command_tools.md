# Command Line Basics
There are a few commands that are absolutely critical to your everyday usage of Rails. In this lesson we will review a few basic ones:

- rails new
- rails server
- rails generate
- rails console
- rails dbconsole
- rails runner

Let's create a simple Rails application to step through each of these commands in context.

# rails new
This is achieved with the 'rails new' command. For instance
```
rails new commandsapp
```

'rails new' autogenerates a default project. It will set you up with what seems like a huge amount of stuff for such a tiny command! You've got the entire Rails directory structure now with all the code you need to run our simple application right out of the box.
This command also has several options you can play with.
```
rails new --help
```

All commands can run with -h or --help to list more information. Please feel free to experiment with them.


# rails server
The rails server command launches a small web server named WEBrick which comes bundled with Ruby. You'll use this any time you want to access your application through a web browser.

With no further work, rails server will run our new shiny Rails app:
```
cd commandsapp
bundle exec rails server
```

You can also use the alias "s" to start the server:
```
bundle exec rails s
```

By default the server runs on port 3000. If you are running it in your own machine for instance, it will be located in localhost:3000
The server can be run on a different port using the -p option.

The default development environment can be changed using -e.
```
bin/rails server -e production -p 4000
```

To check all other options run the command:
```
bin/rails server --help
```

# rails generate
The rails generate command uses templates to create a whole lot of things. Running rails generate by itself gives a list of available generators:

You can also use the alias "g" to invoke the generator command: rails g.
```
bin/rails generate
```

Using generators will save you a large amount of time by writing boilerplate code, writing migrations for your database and other tasks.
Generators are complex so we have dedicated another lessons to show how to work with them, checkot our generators and scaffolding lesson.


# rails console
The console command lets you interact with your Rails application from the command line. On the underside, rails console uses IRB, so if you've ever used it, you'll be right at home. This is useful for testing out quick ideas with code and changing data server-side without touching the website.

You can also use the alias "c" to invoke the console:
```
bin/rails c
```

For instance let's run some code here:
```
Rails.root.to_s
Rails.environment
```
This code gets the directory where our application is installed. In this way we can play with any code.

You can also specify the environment in which the console command should operate.
```
bin/rails console staging
```

If you wish to test out some code without changing any data, you can do that by invoking:
```
bin/rails console --sandbox.
```

Any modifications you make in this mode will be rolled back on exit

# rails dbconsole
rails dbconsole figures out which database you're using and drops you into the command line interface for it (and figures out the command line parameters to give to it, too!). It supports MySQL, PostgreSQL, SQLite and SQLite3.

You can also use the alias "db" to invoke the dbconsole.
```
bin/rails db
```
Here we can do execute any SQL:
```
show tables;
```
And it works!


# rails runner
runner runs Ruby code in the context of Rails non-interactively. For instance:
```
$ bin/rails runner "Rails.environment.to_s"
```
You can also use the alias "r" to invoke the runner: rails r.

You can specify the environment in which the runner command should operate using the -e switch.
```
$ bin/rails runner -e staging "Rails.environment.to_s"
```


This is all for this lesson. Please use some time playing with these commands, if your project gets destroyed, do not worry, delete it and create a new one with 'rails new'. Use the power of the command line!



