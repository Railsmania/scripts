In this lesson we're going to showcase how to use a nifty utility gem called Merrow.
Ruby is a general programming language with many uses outside Rails. Merrow and many other gems provide us utilities for our system.

First let's install the Merrow gem. Since this particular gem is not installed for any specific project, let's use the command 'gem install' instead of adding it to a gemfile and installing it with bundler.

```
gem install merrow
```

Merrow is a utility which scans a list of Github repositories and returns a list of pull requests which are open on those repositories. This will save you a lot of time when you are developing several projects and do not want to manually check every one of them.

Now that Merrow is installed, let's see how it works by executing:
```
merrow
```

When Merrow starts it asks us a few questions, it needs a list of repos, let's just give it the default repo example:
```
JordiPolo/merrow
```
Then it needs our github username and our password.
This information will not be stored but Merrow will create a token with just the privileges it needs to work correctly.
We can see that Merrow has created a Token for us.

Let's execute merrow again.
It checks for open pull requests for the project we set-up but there weren't any.
So let's add some more projects for it to check.
Merrow stores its configuration in ~/.merrow.yml. Let's edit that file:

This file already has the repository we are checking and the token Merrow created for us.
We can add any number of repositories here, let's add the repository for rspec

Now we execute merrow again and sure enough there is a pull requests open for that project!
The more projects you want to check the more useful merrow will be for you!

That's it for this lesson. Be on the lookout for more lessons like this on RailsMania.com that demonstrate other useful gems.