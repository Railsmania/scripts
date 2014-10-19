# Understanding Routes
In this lesson we're going to review the purpose of routes in Ruby on Rails and the pivotal roles they play in your project's architecture. We'll also look at some useful tools to help you inspect the routes in any project to easily understand its structure. And finally we'll cover some best practices in testing routes.

In the config/routes.rb file you'll find all the routes for your project. The Rails router relies on this configuration file to setup all routes for your application. The main purpose of the Rails router is to recognize incoming requests to specific URLs and to dispatch those requests to an action in a controller. The Rails router also helps to autogenerate paths and URLs within your application so that you can avoid hardcoding URL strings in your views.

Let's first look at how Rails routes connects an incoming request to code in your application.

When your Rails application receives an incoming request for:

GET /patients/17
it asks the router to match it to a controller action. If the first matching route is:

get '/patients/:id', to: 'patients#show'
the request is dispatched to the patients controller's show action with { id: '17' } in the special params hash.

One thing to note here, routes are specific to the HTTP verb used for the request. In this example with a GET to patients, the route will only connect the request to the patient's controller if the request was a GET request. If you're not familiar with the various HTTP verbs and their different meanings, please check the lesson notes for a reference.

# Generating Paths and URLs from Code
You can also generate paths and URLs. If this patient route is modified to be:

get '/patients/:id', to: 'patients#show', as: 'patient'
and your application contains this code in the controller:

@patient = Patient.find(17)
and this in the corresponding view:

<%= link_to 'Patient Record', patient_path(@patient) %>
then the router will generate the path /patients/17. This reduces the brittleness of your view and makes your code easier to understand. Note that the id does not need to be specified in the route helper.


# Inspecting and Testing Routes
Rails offers a couple facilities for inspecting and testing your routes.

One of the lesser known but easy ways to get a complete list of the available routes in your application, is by starting a rails server in and visiting the "rails/info/routes" route in your browser. This is only available since Rails 4, older versions of Rails did not provide this feature. On this page you can also experiment with the matchers to see what routes will respond to a particular path.

The most common way to inspect routes is in the terminal. There you can execute the rake routes command to produce the same output.

Both methods will list all of your routes, in the same order that they appear in routes.rb. For each route, you'll see:

The route name (if any)
The HTTP verb used (if the route doesn't respond to all verbs)
The URL pattern to match
The routing parameters for the route

For example, here's a small section of the rake routes output for a RESTful route:

    users GET    /users(.:format)          users#index
          POST   /users(.:format)          users#create
 new_user GET    /users/new(.:format)      users#new
edit_user GET    /users/:id/edit(.:format) users#edit

#Testing Routes
Routes should be included in your testing strategy (just like the rest of your application). Rails offers three built-in assertions designed to make testing routes simpler:

assert_generates
assert_recognizes
assert_routing
5.2.1 The assert_generates Assertion

assert_generates asserts that a particular set of options generate a particular path and can be used with default routes or custom routes. For example:

assert_generates '/photos/1', { controller: 'photos', action: 'show', id: '1' }
assert_generates '/about', controller: 'pages', action: 'about'
5.2.2 The assert_recognizes Assertion

assert_recognizes is the inverse of assert_generates. It asserts that a given path is recognized and routes it to a particular spot in your application. For example:

assert_recognizes({ controller: 'photos', action: 'show', id: '1' }, '/photos/1')
You can supply a :method argument to specify the HTTP verb:

assert_recognizes({ controller: 'photos', action: 'create' }, { path: 'photos', method: :post })
5.2.3 The assert_routing Assertion

The assert_routing assertion checks the route both ways: it tests that the path generates the options, and that the options generate the path. Thus, it combines the functions of assert_generates and assert_recognizes:

assert_routing({ path: 'photos', method: :post }, { controller: 'photos', action: 'create' })
