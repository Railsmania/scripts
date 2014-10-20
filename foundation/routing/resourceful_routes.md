# Resource Routing: the Rails Default
Resource routing allows you to quickly declare all of the common routes for a given resourceful controller. Instead of declaring separate routes for your index, show, new, edit, create, update and destroy actions, a resourceful route declares them in a single line of code.

2.1 Resources on the Web
Browsers request pages from Rails by making a request for a URL using a specific HTTP method, such as GET, POST, PATCH, PUT and DELETE. Each method is a request to perform an operation on the resource. A resource route maps a number of related requests to actions in a single controller.

When your Rails application receives an incoming request for:

DELETE /photos/17
it asks the router to map it to a controller action. If the first matching route is:

resources :photos
Rails would dispatch that request to the destroy method on the photos controller with { id: '17' } in params.

# CRUD, Verbs, and Actions
In Rails, a resourceful route provides a mapping between HTTP verbs and URLs to controller actions. By convention, each action also maps to particular CRUD operations in a database. A single entry in the routing file, such as:

resources :photos
creates seven different routes in your application, all mapping to the Photos controller:

HTTP Verb	Path	Controller#Action	Used for
GET	/photos	photos#index	display a list of all photos
GET	/photos/new	photos#new	return an HTML form for creating a new photo
POST	/photos	photos#create	create a new photo
GET	/photos/:id	photos#show	display a specific photo
GET	/photos/:id/edit	photos#edit	return an HTML form for editing a photo
PATCH/PUT	/photos/:id	photos#update	update a specific photo
DELETE	/photos/:id	photos#destroy	delete a specific photo
Because the router uses the HTTP verb and URL to match inbound requests, four URLs map to seven different actions.

Rails routes are matched in the order they are specified, so if you have a resources :photos above a get 'photos/poll' the show action's route for the resources line will be matched before the get line. To fix this, move the get line above the resources line so that it is matched first.

# Defining Multiple Resources at the Same Time
If you need to create routes for more than one resource, you can save a bit of typing by defining them all with a single call to resources:

resources :photos, :books, :videos
This works exactly the same as:

resources :photos
resources :books
resources :videos

# Singular Resources
Sometimes, you have a resource that clients always look up without referencing an ID. For example, you would like /profile to always show the profile of the currently logged in user. In this case, you can use a singular resource to map /profile (rather than /profile/:id) to the show action:

get 'profile', to: 'users#show'
Passing a String to get will expect a controller#action format, while passing a Symbol will map directly to an action:

get 'profile', to: :show
This resourceful route:

resource :geocoder
creates six different routes in your application, all mapping to the Geocoders controller:

HTTP Verb	Path	Controller#Action	Used for
GET	/geocoder/new	geocoders#new	return an HTML form for creating the geocoder
POST	/geocoder	geocoders#create	create the new geocoder
GET	/geocoder	geocoders#show	display the one and only geocoder resource
GET	/geocoder/edit	geocoders#edit	return an HTML form for editing the geocoder
PATCH/PUT	/geocoder	geocoders#update	update the one and only geocoder resource
DELETE	/geocoder	geocoders#destroy	delete the geocoder resource
Because you might want to use the same controller for a singular route (/account) and a plural route (/accounts/45), singular resources map to plural controllers. So that, for example, resource :photo and resources :photos creates both singular and plural routes that map to the same controller (PhotosController).

A singular resourceful route generates these helpers:

new_geocoder_path returns /geocoder/new
edit_geocoder_path returns /geocoder/edit
geocoder_path returns /geocoder
As with plural resources, the same helpers ending in _url will also include the host, port and path prefix.

A long-standing bug prevents form_for from working automatically with singular resources. As a workaround, specify the URL for the form directly, like so:

form_for @geocoder, url: geocoder_path do |f|


# Nested Resources
It's common to have resources that are logically children of other resources. For example, suppose your application includes these models:

class Magazine < ActiveRecord::Base
  has_many :ads
end
 
class Ad < ActiveRecord::Base
  belongs_to :magazine
end
Nested routes allow you to capture this relationship in your routing. In this case, you could include this route declaration:

resources :magazines do
  resources :ads
end
In addition to the routes for magazines, this declaration will also route ads to an AdsController. The ad URLs require a magazine:

HTTP Verb	Path	Controller#Action	Used for
GET	/magazines/:magazine_id/ads	ads#index	display a list of all ads for a specific magazine
GET	/magazines/:magazine_id/ads/new	ads#new	return an HTML form for creating a new ad belonging to a specific magazine
POST	/magazines/:magazine_id/ads	ads#create	create a new ad belonging to a specific magazine
GET	/magazines/:magazine_id/ads/:id	ads#show	display a specific ad belonging to a specific magazine
GET	/magazines/:magazine_id/ads/:id/edit	ads#edit	return an HTML form for editing an ad belonging to a specific magazine
PATCH/PUT	/magazines/:magazine_id/ads/:id	ads#update	update a specific ad belonging to a specific magazine
DELETE	/magazines/:magazine_id/ads/:id	ads#destroy	delete a specific ad belonging to a specific magazine
This will also create routing helpers such as magazine_ads_url and edit_magazine_ad_path. These helpers take an instance of Magazine as the first parameter (magazine_ads_url(@magazine)).

# Limits to Nesting

You can nest resources within other nested resources if you like. For example:

resources :publishers do
  resources :magazines do
    resources :photos
  end
end
Deeply-nested resources quickly become cumbersome. In this case, for example, the application would recognize paths such as:

/publishers/1/magazines/2/photos/3
The corresponding route helper would be publisher_magazine_photo_url, requiring you to specify objects at all three levels. Indeed, this situation is confusing enough that a popular article by Jamis Buck proposes a rule of thumb for good Rails design:

Resources should never be nested more than 1 level deep.

# Shallow Nesting

One way to avoid deep nesting (as recommended above) is to generate the collection actions scoped under the parent, so as to get a sense of the hierarchy, but to not nest the member actions. In other words, to only build routes with the minimal amount of information to uniquely identify the resource, like this:

resources :posts do
  resources :comments, only: [:index, :new, :create]
end
resources :comments, only: [:show, :edit, :update, :destroy]
This idea strikes a balance between descriptive routes and deep nesting. There exists shorthand syntax to achieve just that, via the :shallow option:

resources :posts do
  resources :comments, shallow: true
end
This will generate the exact same routes as the first example. You can also specify the :shallow option in the parent resource, in which case all of the nested resources will be shallow:

resources :posts, shallow: true do
  resources :comments
  resources :quotes
  resources :drafts
end
The shallow method of the DSL creates a scope inside of which every nesting is shallow. This generates the same routes as the previous example:

shallow do
  resources :posts do
    resources :comments
    resources :quotes
    resources :drafts
  end
end
There exist two options for scope to customize shallow routes. :shallow_path prefixes member paths with the specified parameter:

scope shallow_path: "sekret" do
  resources :posts do
    resources :comments, shallow: true
  end
end
The comments resource here will have the following routes generated for it:

HTTP Verb	Path	Controller#Action	Named Helper
GET	/posts/:post_id/comments(.:format)	comments#index	post_comments
POST	/posts/:post_id/comments(.:format)	comments#create	post_comments
GET	/posts/:post_id/comments/new(.:format)	comments#new	new_post_comment
GET	/sekret/comments/:id/edit(.:format)	comments#edit	edit_comment
GET	/sekret/comments/:id(.:format)	comments#show	comment
PATCH/PUT	/sekret/comments/:id(.:format)	comments#update	comment
DELETE	/sekret/comments/:id(.:format)	comments#destroy	comment
The :shallow_prefix option adds the specified parameter to the named helpers:

scope shallow_prefix: "sekret" do
  resources :posts do
    resources :comments, shallow: true
  end
end
The comments resource here will have the following routes generated for it:

HTTP Verb	Path	Controller#Action	Named Helper
GET	/posts/:post_id/comments(.:format)	comments#index	post_comments
POST	/posts/:post_id/comments(.:format)	comments#create	post_comments
GET	/posts/:post_id/comments/new(.:format)	comments#new	new_post_comment
GET	/comments/:id/edit(.:format)	comments#edit	edit_sekret_comment
GET	/comments/:id(.:format)	comments#show	sekret_comment
PATCH/PUT	/comments/:id(.:format)	comments#update	sekret_comment
DELETE	/comments/:id(.:format)	comments#destroy	sekret_comment


# Adding More RESTful Actions
You are not limited to the seven routes that RESTful routing creates by default. If you like, you may add additional routes that apply to the collection or individual members of the collection.

2.10.1 Adding Member Routes

To add a member route, just add a member block into the resource block:

resources :photos do
  member do
    get 'preview'
  end
end
This will recognize /photos/1/preview with GET, and route to the preview action of PhotosController, with the resource id value passed in params[:id]. It will also create the preview_photo_url and preview_photo_path helpers.

Within the block of member routes, each route name specifies the HTTP verb will be recognized. You can use get, patch, put, post, or delete here . If you don't have multiple member routes, you can also pass :on to a route, eliminating the block:

resources :photos do
  get 'preview', on: :member
end
You can leave out the :on option, this will create the same member route except that the resource id value will be available in params[:photo_id] instead of params[:id].

2.10.2 Adding Collection Routes

To add a route to the collection:

resources :photos do
  collection do
    get 'search'
  end
end
This will enable Rails to recognize paths such as /photos/search with GET, and route to the search action of PhotosController. It will also create the search_photos_url and search_photos_path route helpers.

Just as with member routes, you can pass :on to a route:

resources :photos do
  get 'search', on: :collection
end
2.10.3 Adding Routes for Additional New Actions

To add an alternate new action using the :on shortcut:

resources :comments do
  get 'preview', on: :new
end
This will enable Rails to recognize paths such as /comments/new/preview with GET, and route to the preview action of CommentsController. It will also create the preview_new_comment_url and preview_new_comment_path route helpers.

If you find yourself adding many extra actions to a resourceful route, it's time to stop and ask yourself whether you're disguising the presence of another resource.