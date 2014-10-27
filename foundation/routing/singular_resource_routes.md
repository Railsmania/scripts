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