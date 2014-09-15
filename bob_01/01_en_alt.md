## Bootstrap!
Today, we're going to create a new Rails application and add this free HTML dashboard theme we found. Besides the fact it has a lot of cool graphs and dickey doos, the theme is based on bootstrap, so it's mobile friendly. 

Adding a bootstrap theme to a rails project is very simple, so let's get started!

First, let's open up a command line window to create our Rails project. We're going to name our project "bob". We can create our project by running the ```rails new bob``` command in the directory where we want our project to be created.

Rails new takes a few moments to generate all the base files for your project. Once it's compelete, let's verify everything is ok by starting a development rails http server on localhost.

From the command line run "bundle exec rails server". Now open a new browser tab and go to [insert URL and port here].

We can see the landing page that Rails generated for us. It displays information about the environment that our application is currently running in. Everything is working fine if we can see this landing page.

Our first question: how do we show anything other than the default landing page that rails provides? 

The answer: by adding a route to our routes file. Every URL that rails will respond to must be added to the routes file. Here's what we'll do:

Open up the routes file. Uncomment the root directive. We know we want a cool admin dashboard, so we'll rename this route to go to the dashboard's index route and save it.

Let's refresh our app's browser tab. Notice the landing page is gone. It's been replaced by this lovely error message page. It points out that we have a routing error. The specific issue is that the DashboardsController is missing.

So, let's create our dashboards controller and index.

In the app/controllers directory we want to create a file named dashboards_controller.rb and in that file we'll put the following code:

```
class DashboardsController < ApplicationController
  def index
  end
end
```

The DashboardsController extends the ApplicationController which Rails has already provided us. And our index action has no special logic, so we'll leave it blank.

Now we need to create a view for this action. In the app/views/ directory create a folder named dashboards, plural, just like the controller and route. In there, add an index.html.erb file. Then type in a short message like this:

```
<h1>In Dashboards Index</h1>
```

If we save now, restart our rails server, and view the project in our browser -- we'll see the dashboard's index view and our message.

Great, now it's time to add our bootstrap theme. Most bootstrap themes come with example HTML and all the CSS and Javascript nestled away in subfolders following different naming conventions. That's not a problem for us.

All we have to do is move our CSS to the assets/stylesheets/ directory. Any minified css we'll discard as long as we have the unminified version.

Then move the javascript to assets/javascripts/. Just like the stylesheets, any minified javascript we'll discard as long as we have the unminified version.

And move any images to assets/images/

Finally, if you have any custom fonts, just make a directory inside assets/ named fonts and put them there. Simple!

Ok, now we need to decide where to put this main theme file. I prefer keeping the original application layout file as simple and clean as possible - just to make it easier to swap out themes in different parts of my application. The only thing I want to do in the app/views/layouts/application.html.erb is to move the javascript include tag below to the bottom of the page, right above the closing body tag.

Then take all the body content from our bootstrap theme demo file -- excluding the javascript tags at the bottom --, copy it, and paste it into the /app/views/dashboards/index.html.erb

Save it, then refresh our application in the browser. Wow, looks great, but there are a few things missing. Using our browser's developer tools we can see what's broken. Knowing how to fix it is a little more tricky, but here's the quick answers:

FontAwesome's css file needs to be converted to an erb file, so we can fix some paths using a Rails path helper:

```
  src: url('<%= asset_path("fontawesome-webfont.eot") %>?v=4.1.0');
  src: url('<%= asset_path("fontawesome-webfont.eot") %>?#iefix&v=4.1.0') format('embedded-opentype'), url('<%= asset_path("fontawesome-webfont.woff") %>?v=4.1.0') format('woff'), url('<%= asset_path("fontawesome-webfont.ttf") %>?v=4.1.0') format('truetype'), url('<%= asset_path("fontawesome-webfont.svg") %>?v=4.1.0#fontawesomeregular') format('svg');

````

Next, we need to fix some of the javascript. We'll stub 2 javascript files that are showing errors in our browser's developer tools console. These two files are demo files for the theme we downloaded, we're going to turn them off by using this stub keyword.

```
//= stub plugins/flot/flot-data
//= stub plugins/dataTables/dataTables.bootstrap
```

That will leave them in the project so we can reuse them later, stubbing just tells Rails to ignore them.

Now, refreshing our project in our browser shows no errors and everything appears to be here.

That's it for this lesson, be sure to watch the additional related content and advanced topics we are sharing on RailsMania.com.

--------
### Easter Egg

