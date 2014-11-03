#Gems: Advanced topics

In this lesson we will learn about the Gemfile.lock and other advanced topics like installing gems from different sources and working with locally developed gems.


## Gemfile.lock
When Bundler executes 'bundle install', it does not only install the gems but it updates the 'Gemfile.lock' file. This file is modified automatically by Bundler and should not be modified manually.
Let's take a look at it thought.
This file is a cache of dependencies between Gems and specifies the versions used by this project. While the 'Gemfile' specifies gems and constrains that can be more or less open, the 'Gemfile.lock' has the specific version that fulfill all the constrains and will be downloaded and installed.
For instance, we can see here the specific sqlite version.

You may notice that the Gemfile.lock file has many more gems than our Gemfile. This is because Bundle will install not only the gems we have requested but all their dependencies to make sure the requested gems work as expected.
In practice, we never need to worry about this file. Just execute 'bundle install' and everything works magically.


## Rubygems.org

The first line on this file is:
```
source 'https://rubygems.org'
```

This indicates the Gem server we are using. A Gem server provides a list of gems available for installation. Rubygems.org is the de-facto standard repository where most of the gems are published. Let's take a look at this site.

As we can see there is more than 90.000 gems at the moment of recording this video. We can search for any gem. Let's search for 'Rails'. We got an exact match and more than 3000 gems related to Rails which enhance its functionality.

If we click on the Rails gem we can see some of the information about the gem.
We have the last stable version on the top, the command to install it using Rubygems.

There are some links to resources related to the gem. We recommend you to take a look thought them if you need more information about any specific gem.
We also have here a list of all the version of the gem published till now and its dependencies.

It is not necessary to know all this information but if you ever need to understand how to use all the advanced features of a gem, it is easy to start by searching for the gem in Rubygems.

## Alternative sources

We do not need to use Rubygems for all our gems. Let's see how to install a local gem. We can use a path in our local machine like this:
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
We could install from any branch
```
gem 'kender', git: 'https://github.com/mdsol/kender.git', branch: 'experimental'
```
or tag:
```
gem 'kender', git: 'https://github.com/mdsol/kender.git', tag: 'v0.2.5'
```


In this lesson we have learned about the default source of information for our Gemfile and how to use alternative sources. We have also learned about the Gemfile.lock.



