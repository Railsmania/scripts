#Gems: Advanced topics

In this lesson we'll learn about the Gemfile.lock and other advanced topics like installing gems from different sources and working with locally developed gems.


## Gemfile.lock
When Bundler executes 'bundle install', it not only installs the gems but it also updates the 'Gemfile.lock' file. This file is generated and modified automatically by Bundler and should never be modified manually. Even though we should never modify it manually, let's take a quick look at its contents.

This file is a cache of dependencies between Gems, and it specifies the precise versions of gems used by this project. While the 'Gemfile' specifies gems and their range of constraints, the 'Gemfile.lock' indicates the specific version of a gem that fulfills all the constraints, and that precise version will be downloaded and installed by Bundler.

For instance, we can see here the specific sq lite version.

You may notice that the Gemfile.lock file has many more gems than our Gemfile. This is because Bundler will install not only the gems we have requested but all their dependencies to make sure the requested gems work as expected.
In practice, we never need to worry about this file. Just execute 'bundle install' and everything works magically.


## Rubygems.org

Moving back to the Gemfile, let's look at the first line of the file:
```
source 'https://rubygems.org'
```

This line indicates the Gem server we are using. A Gem server provides a downloadable copy of gems that are available for installation. Rubygems.org is the de-facto standard repository where most of the gems in the Ruby universe are published. Let's take a look at this site.

As we can see there are more than 90.000 gems available at this moment in time. Let's search for the 'Rails' gem. We got an exact match and more than 3000 gems related to Rails which enhance its functionality.

If we click on the Rails gem we can see some useful information about the gem.
We have the last stable version on the top, and the command to install it when using Rubygems.

There are also some links to resources related to the gem. And we have here a list of all the versions of this gem that have been published til now and their dependencies.

It's not necessary to know all this information but if you ever need to understand how to use all the advanced features of a gem, it's easy to start by searching for the gem in Rubygems.

## Alternative sources

As useful and nice as RubyGems.org is, we don't need to use Rubygems for all of our gems. If we happen to be working on our own Gem, we might want to reference the files locally. So, we can use a path in our local machine by using the path parameter:
```
gem 'my_tool', path: '../my_tool'
```
Now when we do
```
bundle install
```

We are installing the gem from our local path. This is very useful when we are developing our gems and want to sanity check our changes inside our project.

We can also install gems from a git repository, for instance:
```
gem 'kender', git: 'https://github.com/mdsol/kender.git'
```

will install the gem from the default branch of that repository.
We could install from any specifc branch as well by specifying the branch parameter.
```
gem 'kender', git: 'https://github.com/mdsol/kender.git', branch: 'experimental'
```
Or we could specify a specific release tag with the tag parameter:
```
gem 'kender', git: 'https://github.com/mdsol/kender.git', tag: 'v0.2.5'
```


In this lesson we've learned about the default source of information for our Gemfile and how to use alternative sources. We also learned about the Gemfile.lock and its purpose. That's it for now, be sure to check out our other lessons on gems and versioning on RailsMania.com.



