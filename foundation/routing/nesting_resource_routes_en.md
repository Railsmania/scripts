[wip]
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