Parent - Child relationship is when 1 model owns 1 or more instances of another model

In my Application the user will be the creator or owner of a car

A user can own many cars, but a car can only be owned by one user

So lets go ahead and make that relationship. Lets go ahead on the Car model and add a new property of type user and annotate it with the parent property wrapper like so:

@Parent(key: "userID")
var user: User

The key in the parent property wrapper maps the column in the table. This now links the models with fluent

Next lets add it to the initializer

The property on car allows us to get a car's user, but what about getting the cars for a user itself

lets add the following on the User model: 

@Children(for: \.$user)
var cars: [Car]

This adds a property for all of the user's cars

The property is annotated with the children property wrapper, which takes a key path to the property linking the models and the parent. In this case cars user

And that is all we need to do to be able to set up a parent-child relationship
