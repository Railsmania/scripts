[wip]
# Testing Routes
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

# Using Rspec
