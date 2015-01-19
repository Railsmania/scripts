#dice_bag

In this lesson we are going to talk about an useful gem to manage the configuration files of a Rails project.
For example, let's take a look at the 'config/database.yml' file. This file contains the configuration for the database we are using, it sets up where to connect to database and the credentials to do so.
Here we can see the default file which Rails creates for our development environment.
We want to have a different database configuration in our production servers because of performance reasons.
Our file in production would look like this:
```
production:
  adapter: mysql2
  database: myproject_production
  username: application
  password: n0mwmd0923
  host: xxxxxx.domain.com
```

We do not want to commit to the Git repository all this secret information. So, how can we create a file that changes when it is deployed in production?

Dicebag can solve this problem.

Let's look at Dicebag's documentation.

First let's install it in our project. Let's add dice_bag
```
gem 'dice_bag', '~> 0.8'
```
to our Gemfile and execute:
```
bundle install
```

Dicebag let's you create a template file which would read the values of some environment variables to modify the contents of the file in production.

Let's make a template file for our database.yml.

The first step is to copy our current database.yml as a database.yml.dice file.
```
cp config/database.yml config/database.yml.dice
```
Now we can delete the real file from git and add the template file instead:
```
git rm config/database.yml
git add config/database.yml.dice
```

All our modifications would go to the template file now.
First, let's make the adapter configurable:
```
adapter: <%= configured.database_driver || 'sqlite' %>
```
This line means that we will read the DATABASE_DRIVER environment variable if it exists or else we will use the string 'sqlite'.

Now that we have a template file, let's create the real database.yml file by calling the config task of our project. In the terminal execute:
```
rake config
```

We can see the database.yml file was created, if we look at the contents we can see that the default 'sqlite' is selected as adaptor.

Now let's run the configuration again, now we will choose a different driver:
```
DATABASE_DRIVER=mysql rake config
```
If we choose to overwrite the file and we open it we can see that now the adaptor is 'mysql'.

In this way, we can keep modifying the template file and make it more dynamic, after some modifications we have a template file that looks like this:
```
<% [:development, :test, :production].each do |env| %>
<%= env %>:
  adapter: <%= configured.database_driver || 'sqllite' %>
  database: <%= configured.database_name || "myproject_#{env}" %>
  username: <%= configured.database_username || 'root' %>
  password: <%= configured.database_password %>
  host: <%= configured.database_host || 'localhost' %>
  pool: 5
  timeout: 5000
  encoding: utf8
<% end %>
```

If you run
```
rake config
```
without any environment variables it will still create a correct file for local development but it can also be configured for a production environment.

That's all, we hope you can use dice_bag in your projects to save some time.





















