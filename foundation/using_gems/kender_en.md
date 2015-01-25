[jordi]
# kender

In this lesson we're going to take a look at how a gem called 'kender' can help you manage all your test suites.

To demonstrate, let's first look at this complex project.
It has many gems in its test group. If we are working with it for the first time, maybe it's difficult to know what we need to run to ensure all the relevant tests are executed.

It would be ideal to have one single command that runs all your tests. That's what Kender does.

Let's install kender in this project. In the Gemfile we add:
```
gem 'kender' '~> 0.2'
```

And execute
```
bundle install
```

Kender has added a couple of new rake tasks. Let's execute the 'ci' task:
```
rake ci
```

This task will run rspec, cucumber and brakeman. If the gem dice_bag is available will use it to configure your project in the test environment before running. We cover dice_bag in another lesson, so be sure to check that out.

Kender orchestrates using all these tools and will only respond with a passing status if all of these tools finish successfully.

Maybe you noticed there is a line in the output saying Kender could not connect to Github. If you provide a github token, Kender will connect to Github and send the result of your tests, either pass or fail, directly to your project there. This is especially useful when you have a pull request and you want to know if those code changes passed all your automated tests. Kender will send that information automatically for you.

When we execute:
```
GITHUB_TOKEN=our_token_here rake ci
```
Kender sends the information to github where the UI shows the result.

Kender tries to be intelligent and does not need any configuration. It's especially useful for your continuous integration server but it's also quite useful for running all your tests in local development.

See you in the next lession.

