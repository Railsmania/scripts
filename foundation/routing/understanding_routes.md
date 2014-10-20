# Understanding Routes
In this lesson we're going to review the purpose and basic usage of routes in Ruby on Rails. We'll also look at some useful tools to help you inspect the routes in any project to easily understand its structure. And finally we'll cover some best practices in testing routes.

In the config/routes.rb file you'll find all the routes for your project. The Rails router relies on this configuration file to setup all routes for your application. The main purpose of the Rails router is to recognize incoming requests to specific URLs and to dispatch those requests to an action in a controller. The Rails router also helps to autogenerate paths and URLs within your application so that you can avoid hardcoding URL strings in your views.

Let's first look at how Rails routes connects an incoming request to code in your application.

When your Rails application receives an incoming request for:

GET /patients/17
Rails asks the router to match this path to a controller action. If the first matching route is:

get '/patients/:id', to: 'patients#show'
the request is dispatched to the patients controller's show action with { id: '17' } in the special params hash.

One thing to note here, routes are specific to the HTTP verb used for the request. In this example with a GET to patients, the route will only connect the request to the patient's controller if the request was a GET request. If you're not familiar with the various HTTP verbs and their different meanings, please check the lesson notes for a reference.

# Inspecting Routes
Rails offers a couple of facilities for inspecting your application's routes.

One of the lesser known but easy ways to get a complete list of the available routes in your application is by starting a rails server and visiting the "rails/info/routes" route in your browser. This is a new feature of Rails 4. On this page you can also experiment with the matchers to see what routes will respond to a particular path.

The most common way to inspect routes is in the terminal. There you can execute the `rake routes` command to produce the same output.

Both methods will list all of your routes, in the same order that they appear in routes.rb. For each route, you'll see something like this:

    users GET    /users(.:format)          users#index
          POST   /users(.:format)          users#create
 new_user GET    /users/new(.:format)      users#new
edit_user GET    /users/:id/edit(.:format) users#edit

On the far right you'll see the routing parameters for the route (what it connects to).

Next, the URL pattern to match. In the firstline  we see that the route matches to the users path. At the end of this format we can see the parenthetical addition "(.:format)". The parenthesis at the end are a conditional match. The dot is there to match a literal dot that you'd usually see in separating a file path to a file type. The colon is used in routes to indicate a variable, so colon-format means that if there is any file extension added to the end of this PATH then the router will store the extension -- excluding the period -- in the params variable named "format". In the last route you can see the "id" variable embedded in the middle of the PATH. So that path expects some special value will be placed there and it will make it available to your application as the param variable "id".

To the left of the URL pattern is the HTTP verb used such as GET, POST, PUT, or DELETE.

And in the first column on the left is the route name if any exists for that route. So the first route to the User's Controller, index action, has the name 'users'. This means that it can be accessed by writing 'users_path' in your views. The second route to the create method on the User's Controller is the same as the first except it requires the request to be a POST. The third route to User's Controller, new action, can be accessed by 'new_user_path' in your views. And similarly, the last can be accessed by the 'edit_user_path' helper method in your view. Using these path helpers are a great way to prevent breaking your view code when you need to refactor application URLs. Let's look at an example of path helpers.


# Generating Paths and URLs from Code
Let's take a another look at how to generate paths and URLs from your routes. Going back to the first route we wrote, let's modify it to be:

get '/patients/:id', to: 'patients#show', as: 'patient'

Checking the routes in the application we see the route listed. And we can see it has the route name of "patient" and that it expects a variable value for the id of the patient.

And assume our application contains this code in the patient controller:

@patient = Patient.find(17)

which simply is hardcoded to find the seventeenth patient and assign it to the instance variable @patient.

Then in any view we can use the route path helper to generate the link for viewing this patient:

<a href="<%= patient_path(@patient) %>">Patient Record</a>
When we inspect the output, we can see that the router generated the path /patients/17. And that is the correct path for viewing that object. Generating the URLs like this reduces the brittleness of your view and makes your code easier to maintain and understand.

Well, that's enough for this lesson's dive into routing. In the next lesson on Rails routes we'll uncover more of the details behind resourceful and non-resourceful routing.
