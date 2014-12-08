# Controller Namespaces, Scoping, and Sharing
You may wish to organize groups of controllers under a namespace. Most commonly, you might group a number of administrative controllers under an Admin:: namespace. You would place these controllers under the app/controllers/admin directory, and you can group them together in your router:

namespace :admin do
  resources :posts, :comments
end
This will create a number of routes for each of the posts and comments controller. For Admin::PostsController, Rails will create:

HTTP Verb	Path	Controller#Action	Named Helper
GET	/admin/posts	admin/posts#index	admin_posts_path
GET	/admin/posts/new	admin/posts#new	new_admin_post_path
POST	/admin/posts	admin/posts#create	admin_posts_path
GET	/admin/posts/:id	admin/posts#show	admin_post_path(:id)
GET	/admin/posts/:id/edit	admin/posts#edit	edit_admin_post_path(:id)
PATCH/PUT	/admin/posts/:id	admin/posts#update	admin_post_path(:id)
DELETE	/admin/posts/:id	admin/posts#destroy	admin_post_path(:id)

If you want to route /posts (without the prefix /admin) to Admin::PostsController, you could use:

scope module: 'admin' do
  resources :posts, :comments
end
or, for a single case:

resources :posts, module: 'admin'
If you want to route /admin/posts to PostsController (without the Admin:: module prefix), you could use:

scope '/admin' do
  resources :posts, :comments
end
or, for a single case:

resources :posts, path: '/admin/posts'
In each of these cases, the named routes remain the same as if you did not use scope. In the last case, the following paths map to PostsController:

HTTP Verb	Path	Controller#Action	Named Helper
GET	/admin/posts	posts#index	posts_path
GET	/admin/posts/new	posts#new	new_post_path
POST	/admin/posts	posts#create	posts_path
GET	/admin/posts/:id	posts#show	post_path(:id)
GET	/admin/posts/:id/edit	posts#edit	edit_post_path(:id)
PATCH/PUT	/admin/posts/:id	posts#update	post_path(:id)
DELETE	/admin/posts/:id	posts#destroy	post_path(:id)
If you need to use a different controller namespace inside a namespace block you can specify an absolute controller path, e.g: get '/foo' => '/foo#index'.

# Routing concerns
Routing Concerns allows you to declare common routes that can be reused inside other resources and routes. To define a concern:

concern :commentable do
  resources :comments
end
 
concern :image_attachable do
  resources :images, only: :index
end
These concerns can be used in resources to avoid code duplication and share behavior across routes:

resources :messages, concerns: :commentable
 
resources :posts, concerns: [:commentable, :image_attachable]
The above is equivalent to:

resources :messages do
  resources :comments
end
 
resources :posts do
  resources :comments
  resources :images, only: :index
end
Also you can use them in any place that you want inside the routes, for example in a scope or namespace call:

namespace :posts do
  concerns :commentable
end
