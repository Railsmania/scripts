[wip]
# Customizing Resourceful Routes
While the default routes and helpers generated by resources :posts will usually serve you well, you may want to customize them in some way. Rails allows you to customize virtually any generic part of the resourceful helpers.

## Specifying a Controller to Use
The :controller option lets you explicitly specify a controller to use for the resource. For example:

resources :photos, controller: 'images'
will recognize incoming paths beginning with /photos but route to the Images controller:

HTTP Verb	Path	Controller#Action	Named Helper
GET	/photos	images#index	photos_path
GET	/photos/new	images#new	new_photo_path
POST	/photos	images#create	photos_path
GET	/photos/:id	images#show	photo_path(:id)
GET	/photos/:id/edit	images#edit	edit_photo_path(:id)
PATCH/PUT	/photos/:id	images#update	photo_path(:id)
DELETE	/photos/:id	images#destroy	photo_path(:id)
Use photos_path, new_photo_path, etc. to generate paths for this resource.

For namespaced controllers you can use the directory notation. For example:

resources :user_permissions, controller: 'admin/user_permissions'
This will route to the Admin::UserPermissions controller.

Only the directory notation is supported. Specifying the
controller with Ruby constant notation (eg. controller: 'Admin::UserPermissions')
can lead to routing problems and results in
a warning.

## Specifying Constraints
You can use the :constraints option to specify a required format on the implicit id. For example:

resources :photos, constraints: {id: /[A-Z][A-Z][0-9]+/}
This declaration constrains the :id parameter to match the supplied regular expression. So, in this case, the router would no longer match /photos/1 to this route. Instead, /photos/RR27 would match.

You can specify a single constraint to apply to a number of routes by using the block form:

constraints(id: /[A-Z][A-Z][0-9]+/) do
  resources :photos
  resources :accounts
end
Of course, you can use the more advanced constraints available in non-resourceful routes in this context.

By default the :id parameter doesn't accept dots - this is because the dot is used as a separator for formatted routes. If you need to use a dot within an :id add a constraint which overrides this - for example id: /[^\/]+/ allows anything except a slash.

## Overriding the Named Helpers
The :as option lets you override the normal naming for the named route helpers. For example:

resources :photos, as: 'images'
will recognize incoming paths beginning with /photos and route the requests to PhotosController, but use the value of the :as option to name the helpers.

HTTP Verb	Path	Controller#Action	Named Helper
GET	/photos	photos#index	images_path
GET	/photos/new	photos#new	new_image_path
POST	/photos	photos#create	images_path
GET	/photos/:id	photos#show	image_path(:id)
GET	/photos/:id/edit	photos#edit	edit_image_path(:id)
PATCH/PUT	/photos/:id	photos#update	image_path(:id)
DELETE	/photos/:id	photos#destroy	image_path(:id)

## Overriding the new and edit Segments
The :path_names option lets you override the automatically-generated "new" and "edit" segments in paths:

resources :photos, path_names: { new: 'make', edit: 'change' }
This would cause the routing to recognize paths such as:

/photos/make
/photos/1/change
The actual action names aren't changed by this option. The two paths shown would still route to the new and edit actions.

If you find yourself wanting to change this option uniformly for all of your routes, you can use a scope.

scope path_names: { new: 'make' } do
  # rest of your routes
end

## Prefixing the Named Route Helpers
You can use the :as option to prefix the named route helpers that Rails generates for a route. Use this option to prevent name collisions between routes using a path scope. For example:

scope 'admin' do
  resources :photos, as: 'admin_photos'
end

resources :photos
This will provide route helpers such as admin_photos_path, new_admin_photo_path etc.

To prefix a group of route helpers, use :as with scope:

scope 'admin', as: 'admin' do
  resources :photos, :accounts
end

resources :photos, :accounts
This will generate routes such as admin_photos_path and admin_accounts_path which map to /admin/photos and /admin/accounts respectively.

The namespace scope will automatically add :as as well as :module and :path prefixes.

You can prefix routes with a named parameter also:

scope ':username' do
  resources :posts
end
This will provide you with URLs such as /bob/posts/1 and will allow you to reference the username part of the path as params[:username] in controllers, helpers and views.

## Translated Paths
Using scope, we can alter path names generated by resources:

scope(path_names: { new: 'neu', edit: 'bearbeiten' }) do
  resources :categories, path: 'kategorien'
end
Rails now creates routes to the CategoriesController.

HTTP Verb	Path	Controller#Action	Named Helper
GET	/kategorien	categories#index	categories_path
GET	/kategorien/neu	categories#new	new_category_path
POST	/kategorien	categories#create	categories_path
GET	/kategorien/:id	categories#show	category_path(:id)
GET	/kategorien/:id/bearbeiten	categories#edit	edit_category_path(:id)
PATCH/PUT	/kategorien/:id	categories#update	category_path(:id)
DELETE	/kategorien/:id	categories#destroy	category_path(:id)

## Overriding the Singular Form
If you want to define the singular form of a resource, you should add additional rules to the Inflector:

ActiveSupport::Inflector.inflections do |inflect|
  inflect.irregular 'tooth', 'teeth'
end

## Using :as in Nested Resources
The :as option overrides the automatically-generated name for the resource in nested route helpers. For example:

resources :magazines do
  resources :ads, as: 'periodical_ads'
end
This will create routing helpers such as magazine_periodical_ads_url and edit_magazine_periodical_ad_path.