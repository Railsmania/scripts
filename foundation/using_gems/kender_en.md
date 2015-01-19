# kender

In this lesson we are going to take a look at the 'kender' gem.

First let's look at this complex project.
We can see it has many gems in its test group. If we do not know well this project maybe is difficult to know what we need to run to ensure all the tests are passing.

It would be ideal to have one single command that runs all your tests. That's what Kender does.

Let's install kender in this project. In the Gemfile we add:
```
gem 'kender' '~> 0.2'
```

And execute
```
bundle install
```

Now we can execute the task that Kender provides:
```
rake ci
```

It run rspec, cucumber and brakeman. Not only that but made sure everything was correctly configured using 'dice_bag'.

Maybe you noticed this line saying it could not connect to Github.

If you provide a github token, Kender will connect to Github and send the result of your tests. This is specially useful when you have a pull request and you want to know if it passed the tests. Kender will send that information automatically for you.

When we execute:
```
GITHUB_TOKEN=asdf rake ci
```
and the tests pass we can see this in Github's UI


As the name implies, Kender is ideal to run in your continuous integration server. Hope this gem will become part of your toolbox and will help you ensure the quality of your project.

