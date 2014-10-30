# Gems: versioning in the Gemfile


In this lesson we'll learn how to version the gems we will be using in our project.
Let's open the Gemfile again and look at the constrains we can add to our gems.
In the default Gemfile created by Rails we can see a few restrictions already in place.

## Pinned down versions

Let's start with the first gem:
```
 gem 'rails', '4.1.6'
```

In this line we are saying we want to install version 4.1.6 of Rails and no other version. If Bundle can not find that specific version it will give back an error saying that Rails could not be found on the version specified.

The next gem:
```
gem 'sqlite3'
```
Does not have any constrain. Without the second parameter, we are saying that any version of 'sqlite3' should work with this project. Bundle will try to install the highest version of this gem it can find.
This is dangerous because 'sqlite3' could release a new version in the future breaking compatibility and we may not discover the problem till after deployment.
If other gems depend on sqlite3, they may constrain the versions you can install but it is a best practice to specify it yourself in the Gemfile.


## Constrained versions

Before we solve this problem, let's look at the next gem:
```
gem 'sass-rails', '~> 4.0.3'
```

This gem is using the '~>'(pronounced twiddle-wakka) operator. The twiddle-wakka operator means that the last number on the version can freely change but any other number is pinned down.
For instance
```
~> 4.0.3
```
Can become
 4.0.4 or 4.0.10 but never 4.1

If we had:
```
~> 4.0
```
Then we could upgrade to 4.1, 4.2, etc but never to 5.0

Knowing this we can constrain the 'sqlite3' gem to something that makes sense:
```
gem 'sqlite3', '~> 1.3'
```

## Groups

This way our project will not attempt to upgrade to version 2.x which probably would be a major version breaking compatibility, but still enjoy updates in the 1.x branch.


Let's look at this other section of the file:
```
gem 'spring',   group: :development
```

This could see this sometimes as:
```
group :development do
  gem 'spring'
end
```


This means the gem 'spring' belongs to the group development. This is important because we can do something like this in the terminal:
```
bundle install --without development
```

which is a very common command when we deploy our application and avoid installing extra software we do not need. As Rails by default will load all the gems at initialization, the less gems we install the faster it will initialize and less problems we may find with third-party software when running our project.

That's all for this lesson. We learned about grouping and versioning of gems, also some best practices for our 'Gemfile' file.

