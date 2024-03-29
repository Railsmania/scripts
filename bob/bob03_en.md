# Open authentication in Rails

In this lesson we're going to require users to authenticate before they can see the dashboard that we're working on.

To do that, we're going to use an Open Authentication strategy, or "Oauth", to log users in to our application. If you're not familiar with Oauth, please check the lesson notes for a thorough explanation.

Today, we're going to use the Omniauth gem and an Oauth strategy gem to allow users to authenticate via DigitalOcean's Oauth service. At the bottom of our gemfile, we'll add "omniauth" and "omniauth-digitalocean" gems, each fixed to a specific version.

```
gem 'omniauth', '1.2.2'
gem 'omniauth-digitalocean', '0.1.0'
```
Then we'll execute the command 'bundle install'.

# BDD review of feature file

Before we start writing any code, you may remember that in our last lesson we added Cucumber to our project so we could follow a test-first method also known as Behavior Driven Development or BDD.

So, let's write the feature scenario that we need to cover for the most basic authentication requirement.

In the "features" directory, create a file named authentication.feature. In it, write the following:

```
Feature: Authentication
  In order to protect sensitive information
  As a user
  I should login before accessing my dashboard

  Scenario: Unauthenticated request
  to a dashboard
    Given I am not logged in
    When I visit the root url
    Then the response should be the login page
```

Now, run "bundle exec cucumber".

Cucumber gives us the recommended step definitions. Copy those and put them into a file named authentication.rb inside the /features/step_definitions directory

So, what does it mean for a user to not be logged in? For our application, a user is not logged in if they have no active session id.

Because by default a user has no active session id, the first step definition can remain blank.

Now the second definition is simple - we just need to have a login page. Our bootstrap theme actually came with a login page, so let's use it.

The sample login page is simple and has very little text. The "Please Sign In" text looks unique enough for us to use as our test's target.

That means, in this step, we can

expect
the page
to
have content
'Please Sign In'

ok, running the scenarios with 'bundle exec cucumber' -- one fails, as expected because we don't have a login page. Time to add one!

# Sessions Controller and Login Page

Create a sessions controller.rb file in "/app/controllers/"
It will inherit from ApplicationController and will have a single method, "new".

Save it, then, create a directory named "sessions" in "/app/views/"

In there, we'll create a "new.html.erb" file and copy in all the body content from the bootstrap theme's login.html. Be sure not to copy the Javascript references though, we already included those in a previous lesson.

We'll make one edit to this template. Since we'll be doing log-in Oauth via DigitalOcean, we need a nice logo on here that people can click to authenticate. This logo is available in the lesson notes, so just add the file to your /app/assets/images directory.

Then, right above the existing HTML form tag, insert a link with this logo.

```
<div class="text-center">
                      <a href="/auth/digitalocean"><%= image_tag 'do_logo.png', width: 250 %><br />Authenticate via Digital Ocean</a>
                    </div>
```

The href for this link is pointing at a route that the omniauth gem provides our rails application. After adding that logo, comment out the regular login form, since we don't need it right now, and save that file.


# Add routes, hook them up to our controller

After reading the omniauth documentation, I know that we'll need these specific routes.

```
  get '/auth/new'
  get '/auth/:provider/callback'
  get '/auth/logout'
  get '/auth/failure'
```

And we'll route each of them to our "sessions" controller.

"Auth/new" will go to "sessions" controller "new" method, and the "auth/provider/callback" will go to a "create" method in "sessions" controller.

While the "auth/logout" route will point to sessions controller destroy, and auth/failure will point to sessions/failure:

```
  get '/auth/new', to: 'sessions#new'
  get '/auth/:provider/callback', to: 'sessions#create'
  get '/auth/logout', to: 'sessions#destroy'
  get '/auth/failure', to: 'sessions#failure'
```

We didn't actually add the "create", "failure", and "destroy" methods to the controller yet, so lets go back and add those now.

In "app/controllers/sessions_controller" add those 3 blank methods

```
class SessionsController < ApplicationController
  def new
  end

  def create
  end

  def destroy
  end

  def failure
  end
end
```

Make sure you save both the controller and routes file.

# Pass our New Scenario

Now let's do the minimum necessary to pass our scenario. All we have to do is redirect everyone to the login page for this to pass. We'll do that using a "before filter" in ApplicationController.

In "app/controllers/application_controller" add a method named 'verify session' then add it to the before_filters. We want this method to redirect to the login page. We can find the route to the login page by opening a terminal and typing:

bundle exec rake routes

There we see that the path to "Sessions#new" is "auth_new_path"

So this method should just redirect to the auth new path.

```
before_filter :verify_session

def verify_session
  redirect_to auth_new_path
end
```
Save the ApplicationController file.

Since we added this "before filter" in the ApplicationController, it will apply to all controllers that inherit from it. To prevent an infinite redirect loop, we need to add a skip before filter to the SessionsController.

Now let's run our scenarios.

The output shows us that our new scenario is now passing. But the scenario we wrote in the previous lesson is now failing. Since we want only authenticated users to access the dashboard, this makes sense.

# Authenticating Users via DigitalOcean
But now we need to write the code so a logged in user can access the dashboard page.

First, we'll need an initializer for Omniauth. In "config/initializers" directory create a file "01_omniauth.rb" and add this code:

```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :digitalocean, 'CLIENT_ID', 'SECRET_TOKEN', scope: "read write"
end
```

The two placeholder strings are for your client_id and secret_token that you got from DigitalOcean. In the lesson notes on RailsMania.com we show what you need to do to get those, so be sure to check that out if you haven't gotten them yet.

I'll add my client id and secret token and save this file.

[Open SessionsController]
Ok, now it's time to hook everything up. Basically, we want a user to click the login logo, authenticate with Digital Ocean, and redirect to our application. When they redirect, they'll land on the "SessionsController#create" method. Here we want to find an existing session that matches, or create a new one based on the authentication hash -- as long as it has a truthy value.

```
if auth_hash
  session_instance = Session.find_or_create_by(content: auth_hash.to_json)
  session[:active] = session_instance.id
end
redirect_to root_path
```
And we'll add this protected helper method:

```
protected
def auth_hash
  request.env['omniauth.auth']
end
```
There are 3 things we need to cover about this code.

First, the special session[] hash is provided to us by Rails. This special hash persists information between requests. Anything we put into it in this request will be around in the next request assuming we don't intentionally delete it and assuming the user doesn't delete their cookies.

Second, the "request.env" is another special hash provided to us by Rails. It contains a lot of information about the current request environment. Omniauth has placed a special payload of information there about the user that just authenticated via our Open Authentication provider -- Digital Ocean.

And Third, we haven't yet talked about a "Session" object, but in this method we're finding or creating an instance of a "Session". The Session object is a model that we need to create. We'll use it to save session information to the database for easy lookup.

So let's create that model by opening a terminal and typing

```
bundle exec rails generate model Session content:text
```
Followed by
```
bundle exec rake db:migrate
```

That created our session model, database migration, and then we created the table by running the migrations.

# Cleanup old code
Next, let's go back to our ApplicationController. That quick code we wrote earlier to pass our scenario needs to be updated. Change it to match the following:

```
redirect_to auth_new_path unless Session.exists?(id: session[:active])
```

Now we're only going to redirect to the login page if there isn't a Session instance with id equal to the value in session[:active].

# Real Test
Ok, time to try it all out. Open up a terminal, make sure you are in your project directory, then start your server:

```
bundle exec rails server
```

Open your server's URL in your browser and...

Great! it redirected us to the login page. Clicking the link takes us to DigitalOcean where we authenticate. DigitalOcean then sends us back to our application - and we're logged in, Success!


# Two More Things

This is great. Now, let's do two last things.

First, let's make this "logout" link work. We'll edit /app/views/dashboards/index.html.erb to add the auth logout path to that link. We'll also update the "Sessions destroy" method to properly destroy our session when we click logout:

```
Session.destroy(session[:active])
reset_session
redirect_to auth_new_path
```
"reset_session" is another thing rails provides us. It effectively destroys the session as far as rails is concerned. We destroy our own session record in the database and then redirect to the login page. We can test this quite simply by clicking logout and then trying to access the dashboard.

# Pass that scenario!

And finally, when we check our scenarios:

```
bundle exec cucumber
```

we see that one is still failing. We need to get that one failing scenario to pass. Here's how.

Add a new step to the dashboard.feature

```
Given I am logged in
```

and add this definition to our authentication.rb file in features/step_definitions:
```
Given(/^I am logged in$/) do
  OmniAuth.config.test_mode = true
  visit '/auth/digitalocean'
end
```

Run our scenarios, and now they pass as expected.

# End
Alright, that's all we can pack into one lesson. If you have questions or comments, please contact us on RailsMania.com. Thanks for watching!














