[wip]
# Rake
In this lesson we are going to talk about the gem Rake which is installed by default together with ruby. Rake is a standalone Ruby utility that replaces the Unix utility 'make'. It uses a 'Rakefile' and .rake files to build up a list of tasks.

In Rails, Rake is used for common administration tasks, especially sophisticated ones that build off of each other.

You can get a list of Rake tasks available to you, by typing rake --tasks. Each task has a description, and should help you find the thing you need.

```
bundle exec rake --tasks
```

As we can see Rails comes with lots of tasks already. Let's explore a few of these tasks. 	

# about
Let's execute the first of the tasks:
```
bundle exec rake about
```

'rake about' gives version numbers for Ruby, RubyGems, Rails, the Rails subcomponents, will also show all the middlewares used in your application, your database adaptor and current schema version. This information is useful when you need to ask for help or need to check if a security patch might affect you.

# db
The most common rake tasks used in Rails applications are found in the db: Rake namespace. You'll use migrate regularly, but it'll definitely pay off to try out all of the migration rake tasks (up, down, redo, reset).

Since the database rake commands are so important to Rails development, we've prepared another more in-depth series of lessons specifically about them - so be sure to watch those to learn more.

# routes
Likewise, rake routes is a very important tool for Rails developers when dealing with application routing. This rake task will inspect your application and will list all of its defined routes. This task is useful for tracking down routing problems in your app, or giving you a good overview of the URLs in an app you're trying to get familiar with.

# Miscellaneous
A couple miscellaneous tasks here that we'll mention are "rake tasks" and "rake secret"

"rake tasks" can be used for looking at some basic application code statistics, running it will display things information such as KLOCs, thousands of lines of code, and your code to test ratio.

"rake secret" will give you a pseudo-random key that you can use for your session secret.

# Custom Rake Tasks
It's also possible to write your own rake tasks for your application.

Custom rake tasks have a .rake extension and are placed in Rails.root/lib/tasks. You can create these custom rake tasks with the bin/rails generate task command.

desc "I am short, but comprehensive description for my cool task"
task task_name: [:prerequisite_task, :another_task_we_depend_on] do
  # All your magic here
  # Any valid Ruby code is allowed
end
To pass arguments to your custom rake task:

task :task_name, [:arg_1] => [:pre_1, :pre_2] do |t, args|
  # You can use args from here
end
You can group tasks by placing them in namespaces:

namespace :db do
  desc "This task does nothing"
  task :nothing do
    # Seriously, nothing
  end
end
Invocation of the tasks will look like:

$ bin/rake task_name
$ bin/rake "task_name[value 1]" # entire argument string should be quoted
$ bin/rake db:nothing

If your need to interact with your application models, perform database queries and so on, your task should depend on the environment task, which will load your application code.

