# Leveraging RESTful web APIs with Faraday

### What is Faraday
In this lesson we're going to use a Ruby gem named Faraday, which is an HTTP client library. We'll use it to programatically access a RESTful API. After finishing this lesson, you should have a basic understanding of how to add Faraday to your Rails projects, and how to make basic HTTP requests from your Rails project to interact with remote web APIs.

Our basic goal for this lesson will be accessing Digital Ocean's RESTful API and displaying some data about our cloud instances in our admin dashboard.

So let's get started.


### Adding the Gem
The first thing we need to do is add the Faraday gem to our Gemfile. We're specifying the version we want to use, to ensure you have the same experience as shown in this video we recommend that you use the same version as we are.

Once we've added that to the Gemfile, save the file, then run ```bundle install``` from the terminal.

### DropletsController
(https://github.com/Railsmania/bob/commit/e464df5a2a72dc4f940f862490d10e082bc1c9f7)
Now that the gem bundle has installed, let's add a controller for interacting with our Digital Ocean droplets. Create a file in app/controllers/ named "droplets_controller.rb". Let's just make that an empty controller class:

```
class DropletsController < ApplicationController
end
```

Then quickly add a droplets resource route to the config/routes.rb file.

```
resources :droplets
```
You can learn more about resourceful routing in the lesson notes, so be sure to review the links and additional material that we provide there.

Now we want to write some basic controller code to verify that we're able to connect to Digital Ocean's API and make requests. So, in the DropletsController, let's create an index action.

```
def index
end
```

And in that action, add the example code from Faraday's readme for configuring a new connection.

```
conn = Faraday.new(:url => 'https://api.digitalocean.com/v2/') do |faraday|
  faraday.request  :url_encoded             # form-encode POST params
  faraday.response :logger                  # log requests to STDOUT
  faraday.adapter  Faraday.default_adapter  # make requests with Net::HTTP
end
```

Then let's set one header for authorizing our request to Digital Ocean, and assign the result of the request to an instance variable:

```
conn.headers['Authorization'] = "Bearer #{JSON.parse(Session.where(id: session[:active]).first.content)['credentials']['token']}"
@droplets = JSON.parse(conn.get('droplets').body)
```
In a previous lesson, we stored authorization credentials from Digital Ocean when the user logged in using Open Authentication. So we're simply pulling the active session and retrieving the credential token that Digital Ocean provided us.

This is a little verbose, so let's refactor that into an application helper that will be available to all our controllers.

```application_controller.rb
+  def current_session_auth
+    JSON.parse(Session.where(id: session[:active]).first.content)
+  end
```
```droplets_controller.rb
conn.headers['Authorization'] = "Bearer #{current_session_auth['credentials']['token']}"
```

Next, let's create a droplets directory in the views folder. And in there create an index.html.erb with some basic placeholder HTML. The code for this file is in the lesson notes, so be sure to copy from there.

```
<div id="wrapper">
  <%= render partial: 'sidebar'%>

    <div id="page-wrapper">
        <div class="row">
            <div class="col-lg-12">
                <h1 class="page-header">Droplets</h1>
            </div>
            <!-- /.col-lg-12 -->
        </div>
        <%= @droplets %>
    </div>
</div>
```

Ok, let's start our Rails server, and take a look at what we've got so far. After logging into our application, we can append "droplets" to the URL to visit the index action of the droplets controller.

So at this point we're just printing out the entire JSON response from Digital Ocean's API. It looks like we're getting the data we want, so now it's time to format the way we display it to make it look nicer.


### Ocean Model
Now, let's refactor this faraday code from the controller into a special Ocean class that we'll create  in the models directory. You can get this code from the lesson notes:

```
class Ocean
  attr_accessor :droplets, :images

  def initialize(params = {})
    @conn = Faraday.new(:url => 'https://api.digitalocean.com/v2/') do |faraday|
      faraday.request  :url_encoded             # form-encode POST params
      faraday.response :logger                  # log requests to STDOUT
      faraday.adapter  Faraday.default_adapter  # make requests with Net::HTTP
    end
    @conn.headers['Authorization'] = "Bearer #{params[:auth_token]}"
  end

  def droplets
    JSON.parse(@conn.get('droplets').body)['droplets']
  end
end
```

And in the application controller, let's add another method for quickly accessing our Digital Ocean class instance.

```
def ocean
  @ocean ||= Ocean.new(auth_token: current_session_auth['credentials']['token'])
end
```

Here you'll notice something you probably haven't seen before. After the "@ocean" variable we have two vertical bars (called pipes) and an equal sign followed by the instantiation of a new Ocean class. Those two vertical bars and the equal are a special assignment operator in Ruby, known as the conditional assignment operator. Basically, if the "ocean" instance variable is nil or false, then the right side of the operation will be evaluated and assigned to "ocean". If the instance variable "ocean" is not nil or false, then the right side of the conditional assignment operation will not be evaluated - and the program will just keep the current value of "ocean" as it is.

Since we'll be using this method and the one above it in our view files, we'll need to tell Rails that these will be considered "helper" methods.

```
helper_method :current_session_auth, :ocean
```

Methods that need to be accessed in both controllers and views need to be designated as helper_method in your controller. You'll learn more about helper methods in another lesson.

### Showing it off, view changes

Time to inspect our progress. Let's modify the app/views/dashboards/index.html.erb file to dynamically display the number of server instances in our Digital Ocean cloud:

```
- <div class="huge">0</div>
+ <div class="huge"><%= ocean.droplets.count %></div>
```

Then, navigate to the dashboards index and refresh to see the new dynamic content.

Oops, looks like we left something out. Actually this was a bug we introduced when refactoring that prototype code. When we moved code into the application controller, we forgot to refactor this piece in our Model. We're now injecting this authorization dependency through the method parameter. So we simply need to reference that parameter in our model.

Fix that, save the file, and let's refresh our page.

Great, we've managed to connect to Digital Ocean's RESTful API using Faraday and dynamically display the number of server instances we have.

We've really just scratched the surface of what is possible with their API. Displaying information is very simple, but so is interacting with your cloud instances programmatically through Faraday and Digital Ocean's API. Check this lesson's notes for some examples of what you could do, and perhaps try working with a few of their API commands yourself to see what you can come up with.

Thanks for watching!