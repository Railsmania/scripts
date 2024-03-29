# BDD with Cucumber and Capybara
Today, we'll add some fundamental automated testing tools so we can ensure we're delivering working features that do exactly what we wanted them to. If you're not familiar with Behavior Driven Development (often called BDD) or Test Driven Development, I encourage you to learn more about it by looking at this lesson's notes.

In this lesson, we're going to keep it simple so you can follow along even if you're not familiar with BDD, so let's get started!

#### First, add the gems:
First let's open up the Gemfile for the project we made in our previous lesson. The gemfile is a list of all the non-standard Ruby libraries your application depends on. As you can see, even Rails is listed in the gemfile!

At the bottom, we want to create a group of gems that are only available to our development and test environments.

The three gems that we want to include are:
Rspec-rails
Cucumber-rails
and
Database_cleaner

The number after each of these gems defines the precise version we'd like to use. To ensure you have the same experience as captured in this video lesson - be sure you lock each gem to the exact same version as we have here.

```
group :development, :test do
  gem 'rspec-rails', '3.0.2'
  gem 'cucumber-rails', '1.4.1'
  gem 'database_cleaner', '1.3.0'
end
```

Now let's save this file.

After modifying the Gemfile we have to tell our project to recheck and gather any dependencies that we didn't gather previously.

So now we need to open up a console and run:

```
bundle install
```

Bundler will fetch our 3 new dependencies, and any dependencies that they might have, and install them for our application to use.


#### Now install cucumber - doing this generates files needed for running your scenarios.

Once bundler has finished installing our gems, we need to complete a single installation step that cucumber requires. Simply type

```
rails generate cucumber:install
```

And hit enter.

Cucumber creates the necessary files and folders for our project and now it's time to verify everything is installed correctly.

To do that, let's run

#### Make sure it works:
```
bundle exec rake cucumber
```

We don't see much at all, but at least there are no errors. The console output does mention that there are zero scenarios with zero steps, and that's exactly right because we have not written any yet.

```
Using the default profile...
0 scenarios
0 steps
0m0.000s
```

#### Write our first declarative scenario:
So now it's time to actually write our first scenario.

Create a file in /features/ named dashboards.feature

In that file, we'll start with the keyword Feature: then follow that with a single word description.

Starting with an indent on the second line, we'll type a short user story explaining what we're trying to do.

In order to quickly see current activity
A user
Should access a central dashboard

That's what we want this feature file to test for. Now, we need to write a scenario that tests this feature. 

So we'll add a simple scenario that covers the basic requirement that a user can access a central dashboard.

When I visit the root url
Then the response should be the central dashboard

That's it.

```
Feature: Dashboard
  In order to quickly see current activity
  A user
  Should access a central dashboard

  Scenario: Access the dashboard
    When I visit the root url
    Then the response should be the central dashboard
```

Our next step is to try rerunning the cucumber command. So, in the terminal, run 
```
bundle exec rake cucumber
```

#### Add steps file
This time the output is different. Cucumber attempted to run the scenario we wrote, but didn't know what commands to execute to actually complete each step.

Thankfully, cucumber has suggested to us what we can use for step definitions.

So, copy the suggested steps, create a file called dashboard.rb in our /features/step_definitions directory, and paste the suggested content in there. 

Save that file, then run the scenarios again to see the difference:
```
bundle exec rake cucumber
```

#### Modify steps
This time cucumber realizes we have step definitions, but that we haven't customized them to test exactly what we want them to test.

Now all we have to do is write a little code for testing.

In our first step, we just want Cucumber to visit the root path of our application.

And in the second step, we want Cucumber to verify that the HTML response has some visible HTML content that we can identify as the central dashboard.

For now we're going to say that the we
expect the page to have content 'Dashboard'
Let's save that file.
```
When(/^I visit the root url$/) do
  visit root_path
end

Then(/^the response should be the central dashboard$/) do
  expect(page).to have_content 'Dashboard'
end
```

Then run our cucumber scenario again to make sure everything passes.

Viola, they pass. But let's make sure that we're not being tricked -- let's see if it really is testing the page's content. We can do that by changing 'Dashboard' to something we know isn't on that page like 'RailsMania'

Running the tests again we see the scenario failed, and it even printed out that it expected to find 'railsmania' but it wasn't found in all the visible HTML content.

Great, we're all setup to starting developing this application following a Behavior Driven Development (or BDD) style. In the next lesson we'll show the scenario we wrote for our next feature before we get busy writing the code to implement it.

If you want to learn more about how cucumber works and many of its little-known features, check out the in-depth lesson on RailsMania.com!

Thanks for watching. 

