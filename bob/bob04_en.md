# Understanding Layouts, Views, and Partials

### Look at the goal
We've made significant progress with our Ruby on Rails project. In previous lessons we added a Bootstrap theme, wrote some basic automated tests, and provided a method for users to login to our project.

In this lesson, we're going to quickly refactor our project's ERB files where the code to create HTML resides. We'd like our dashboard to look more like this mockup. We want to remove all markup we will not use and replace it with a visual placeholder for displaying the number of servers that are running in our Digital Ocean cloud.

### Understanding Rails' views

There are three types of files under the app/views directory:
- Layouts, living under app/views/layouts
- Views, typically living under a directory named after the controller which renders them.
- Partials, living anywhere and which filename always start with "_" (underscore)

When the user visit a URL in your project, Rails finds the controller that will respond to the request and the controller will typically render a view and send it back to the user as HTML. 
When a view is rendered, if the view uses any partials, they will be rendered and inserted in the HTML of the view. Also, a view typically belongs to a layout which will be rendered as well when the response is composed.

Let's take a closer look at each of these type files.

### Understanding Layouts

Layouts provide a place to put HTML markup that will be repeated across most or all of your web application's pages. This file should thus contain markup that can be applied to all your application. For instance links to your stylesheets.
Ruby on Rails likes to encourage the DRY principle (or "Don't Repeat Yourself") and this layout serves to DRY up our view files.

At this point, our project has a default layout file - we can find it in "/app/views/layouts/index.html.erb". In this file there are only 4 method calls: "stylesheet_link_tag" for including stylesheets from the assets directory, "csrf_meta_tags" for preventing "CSRF" attacks (view the lesson notes to learn more about that), "yield" for displaying content in the page (more on that in a moment), and "javascript_include_tag" for including javascript assets in our HTML response.

The most important method in this page is the "yield" method. When this layout is rendered, this method will be replaced by all the HTML of the view associated with the current request's Controller and Action. For instance, when a request is sent to the Dashboards Controller's Index action, this "yield" will render the "index" view from the "/app/views/dashboards/" directory.

Let's move on to the Dashboards Controller's "index" view file.

### Refactoring Our View
The "/app/views/dashboards/index.html.erb" file is where the majority of our theme's HTML currently exists. It's a rather large file with a lot of markup. Breaking this view into smaller reusable components will help us edit and maintain our application.

The first component we can break away from this view is the top navigation and sidebar. We'll reuse this HTML on many different pages of our application, so it makes sense to separate it.

So, cut this HTML from the "index" view.

Then, create a directory named "application" in "/app/views/". In that directory, create a new file named "_sidebar.html.erb". 

### Partials
That underscore is an important convention in Rails. It differentiates a standard view file from a partial view file. A partial view is a snippet of reusable markup that you may render one or more times in any number of views or layout files. Basically, a partial is the smallest presentation building block in a Rails application. A layout wraps one view, and a view may include one or many partials.

Ok, now that we have our first partial, let's paste into it the HTML we copied from the index view. Save that file. 

Then, go back to to the Dashboard's Index file and and add this snippet of code:

```
<%= render partial: 'sidebar'%>
```

Save it. Then, start a development server in the console by using the

```
bundle exec rails server
```

command. Open your browser and review your project. It should look the same as the last time we left it.

Perfect. We can comment out our partial in the view file, save it, then refresh our browser to see what the page would look like without it.

Ok, uncomment that line, everything is rendering completely again. Now let's refactor one more large piece out of here.

All this demo content, with these graphs and tables, should be moved into a separate file for now. Cut this content. Then create another file in "/app/views/application/" named "underscore demo.html.erb". Paste that content into this demo partial. Save the file, then add this snippet of code to the Dashboard's Index file.

```
<%=# render partial: 'demo' %>
```

We're leaving this one commented out for now. After saving this file and refreshing our browser, we see that all the demo content is now hidden.

Ok, now the last thing we'll do is make the remaining HTML match our mockup design.

Let's comment out these other dashboard UI items.

Then, modify the comments block to instead look like a cloud. We're using Bootstrap, so that's as simple as changing the CSS class of this HTML element. While we're there, let's change this text as well:

```
                      <div class="panel-heading">
                         <div class="row">
                             <div class="col-xs-3">
-                                <i class="fa fa-comments fa-5x"></i>
+                                <i class="fa fa-cloud fa-5x"></i>
                             </div>
                             <div class="col-xs-9 text-right">
-                                <div class="huge">26</div>
-                                <div>New Comments!</div>
+                                <div class="huge">0</div>
+                                <div>Server Instances</div>
                             </div>
                         </div>
                     </div>

```

Ok, we've learned about the view structure in a Rails project and did a little refactoring. If you have questions about layouts, views, or partials feel free to leave a comment on RailsMania.com.

Thanks for watching!



