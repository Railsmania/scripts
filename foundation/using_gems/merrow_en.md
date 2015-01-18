Hi,

In this lesson we are going to talk about about a gem called Merrow.
Ruby is a general programming language with many uses outside Rails. Merrow and many other gems provide us utilities for our system.

First let's install the Merrow gem, as we want to install it in our system and it is not project specific, let's use the command 'gem install':

```
gem install merrow
```

This takes a while.
Merrow is an utility which scan a list of repositories and returns a list of pull requests which are open on those repositories. This will save you a lot of time when you are developing several projects and do not want to manually check every one of them.

Once Merrow is installed, let's execute it by simply writing:
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
It checks the pull request for the project we set-up and there was none.
Let's check some more projects.
Merrow stores its configuration in ~/.merrow.yml. Let's edit that file:

This file already has the repository we are checking and the token Merrow created for us.
We can add any number of repositories here, let's add the repository for rspec

Now we execute merrow again and sure again there is a pull requests open for that project!
The more projects you want to check the more useful merrow will be for you!

There are many Ruby tools you can install, in fact there are two you probably already has installed!
The 'rails' tool which can be used to create new rails projects (among other functionalities) and the 'rake' tool which is a ruby tool to execute tasks.

In Railsmania we will show you other tools to improve your system and make you more efficient in your work.
