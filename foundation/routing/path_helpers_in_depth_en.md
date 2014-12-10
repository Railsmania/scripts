[wip]
# Path and URL Helpers
Creating a resourceful route will also expose a number of helpers to the controllers in your application. In the case of resources :photos:

photos_path returns /photos
new_photo_path returns /photos/new
edit_photo_path(:id) returns /photos/:id/edit (for instance, edit_photo_path(10) returns /photos/10/edit)
photo_path(:id) returns /photos/:id (for instance, photo_path(10) returns /photos/10)
Each of these helpers has a corresponding _url helper (such as photos_url) which returns the same path prefixed with the current host, port and path prefix.

# Creating Paths and URLs From Objects
In addition to using the routing helpers, Rails can also create paths and URLs from an array of parameters. For example, suppose you have this set of routes:

resources :magazines do
  resources :ads
end
When using magazine_ad_path, you can pass in instances of Magazine and Ad instead of the numeric IDs:

<%= link_to 'Ad details', magazine_ad_path(@magazine, @ad) %>
You can also use url_for with a set of objects, and Rails will automatically determine which route you want:

<%= link_to 'Ad details', url_for([@magazine, @ad]) %>
In this case, Rails will see that @magazine is a Magazine and @ad is an Ad and will therefore use the magazine_ad_path helper. In helpers like link_to, you can specify just the object in place of the full url_for call:

<%= link_to 'Ad details', [@magazine, @ad] %>
If you wanted to link to just a magazine:

<%= link_to 'Magazine details', @magazine %>
For other actions, you just need to insert the action name as the first element of the array:

<%= link_to 'Edit Ad', [:edit, @magazine, @ad] %>
This allows you to treat instances of your models as URLs, and is a key advantage to using the resourceful style.