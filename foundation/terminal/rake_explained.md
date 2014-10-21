# Rake
Rake is Ruby Make, a standalone Ruby utility that replaces the Unix utility 'make', and uses a 'Rakefile' and .rake files to build up a list of tasks. In Rails, Rake is used for common administration tasks, especially sophisticated ones that build off of each other.

You can get a list of Rake tasks available to you, which will often depend on your current directory, by typing rake --tasks. Each task has a description, and should help you find the thing you need.

To get the full backtrace for running rake task you can pass the option --trace to command line, for example rake db:create --trace.

$ bin/rake --tasks
rake about              # List versions of all Rails frameworks and the environment
rake assets:clean       # Remove compiled assets
rake assets:precompile  # Compile all the assets named in config.assets.precompile
rake db:create          # Create the database from config/database.yml for the current Rails.env
...
rake log:clear          # Truncates all *.log files in log/ to zero bytes (specify which logs with LOGS=test,development)
rake middleware         # Prints out your Rack middleware stack
...
rake tmp:clear          # Clear session, cache, and socket files from tmp/ (narrow w/ tmp:sessions:clear, tmp:cache:clear, tmp:sockets:clear)
rake tmp:create         # Creates tmp directories for sessions, cache, sockets, and pids
You can also use rake -T to get the list of tasks.

# about
rake about gives information about version numbers for Ruby, RubyGems, Rails, the Rails subcomponents, your application's folder, the current Rails environment name, your app's database adapter, and schema version. It is useful when you need to ask for help, check if a security patch might affect you, or when you need some stats for an existing Rails installation.

$ bin/rake about
About your application's environment
Ruby version              1.9.3 (x86_64-linux)
RubyGems version          1.3.6
Rack version              1.3
Rails version             4.1.4
JavaScript Runtime        Node.js (V8)
Active Record version     4.1.4
Action Pack version       4.1.4
Action View version       4.1.4
Action Mailer version     4.1.4
Active Support version    4.1.4
Middleware                Rack::Sendfile, ActionDispatch::Static, Rack::Lock, #<ActiveSupport::Cache::Strategy::LocalCache::Middleware:0x007ffd131a7c88>, Rack::Runtime, Rack::MethodOverride, ActionDispatch::RequestId, Rails::Rack::Logger, ActionDispatch::ShowExceptions, ActionDispatch::DebugExceptions, ActionDispatch::RemoteIp, ActionDispatch::Reloader, ActionDispatch::Callbacks, ActiveRecord::Migration::CheckPending, ActiveRecord::ConnectionAdapters::ConnectionManagement, ActiveRecord::QueryCache, ActionDispatch::Cookies, ActionDispatch::Session::CookieStore, ActionDispatch::Flash, ActionDispatch::ParamsParser, Rack::Head, Rack::ConditionalGet, Rack::ETag
Application root          /home/foobar/commandsapp
Environment               development
Database adapter          sqlite3
Database schema version   20110805173523
# assets
You can precompile the assets in app/assets using rake assets:precompile and remove those compiled assets using rake assets:clean.

# db
The most common tasks of the db: Rake namespace are migrate and create, and it will pay off to try out all of the migration rake tasks (up, down, redo, reset). rake db:version is useful when troubleshooting, telling you the current version of the database.

More information about migrations can be found in the Migrations guide.

# doc
The doc: namespace has the tools to generate documentation for your app, API documentation, guides. Documentation can also be stripped which is mainly useful for slimming your codebase, like if you're writing a Rails application for an embedded platform.

rake doc:app generates documentation for your application in doc/app.
rake doc:guides generates Rails guides in doc/guides.
rake doc:rails generates API documentation for Rails in doc/api.
# notes
rake notes will search through your code for comments beginning with FIXME, OPTIMIZE or TODO. The search is done in files with extension .builder, .rb, .erb, .haml and .slim for both default and custom annotations.

$ bin/rake notes
(in /home/foobar/commandsapp)
app/controllers/admin/users_controller.rb:
  * [ 20] [TODO] any other way to do this?
  * [132] [FIXME] high priority for next deploy
 
app/models/school.rb:
  * [ 13] [OPTIMIZE] refactor this code to make it faster
  * [ 17] [FIXME]
If you are looking for a specific annotation, say FIXME, you can use rake notes:fixme. Note that you have to lower case the annotation's name.

$ bin/rake notes:fixme
(in /home/foobar/commandsapp)
app/controllers/admin/users_controller.rb:
  * [132] high priority for next deploy
 
app/models/school.rb:
  * [ 17]
You can also use custom annotations in your code and list them using rake notes:custom by specifying the annotation using an environment variable ANNOTATION.

$ bin/rake notes:custom ANNOTATION=BUG
(in /home/foobar/commandsapp)
app/models/post.rb:
  * [ 23] Have to fix this one before pushing!
When using specific annotations and custom annotations, the annotation name (FIXME, BUG etc) is not displayed in the output lines.

By default, rake notes will look in the app, config, lib, bin and test directories. If you would like to search other directories, you can provide them as a comma separated list in an environment variable SOURCE_ANNOTATION_DIRECTORIES.

$ export SOURCE_ANNOTATION_DIRECTORIES='spec,vendor'
$ bin/rake notes
(in /home/foobar/commandsapp)
app/models/user.rb:
  * [ 35] [FIXME] User should have a subscription at this point
spec/models/user_spec.rb:
  * [122] [TODO] Verify the user that has a subscription works
# routes
rake routes will list all of your defined routes, which is useful for tracking down routing problems in your app, or giving you a good overview of the URLs in an app you're trying to get familiar with.

# test
A good description of unit testing in Rails is given in A Guide to Testing Rails Applications

Rails comes with a test suite called Minitest. Rails owes its stability to the use of tests. The tasks available in the test: namespace helps in running the different tests you will hopefully write.

# tmp
The Rails.root/tmp directory is, like the *nix /tmp directory, the holding place for temporary files like sessions (if you're using a file store for files), process id files, and cached actions.

The tmp: namespaced tasks will help you clear and create the Rails.root/tmp directory:

rake tmp:cache:clear clears tmp/cache.
rake tmp:sessions:clear clears tmp/sessions.
rake tmp:sockets:clear clears tmp/sockets.
rake tmp:clear clears all the three: cache, sessions and sockets.
rake tmp:create creates tmp directories for sessions, cache, sockets, and pids.
# Miscellaneous
rake stats is great for looking at statistics on your code, displaying things like KLOCs (thousands of lines of code) and your code to test ratio.
rake secret will give you a pseudo-random key to use for your session secret.
rake time:zones:all lists all the timezones Rails knows about.
# Custom Rake Tasks
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