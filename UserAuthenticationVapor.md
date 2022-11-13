### Discord Advise

technically basic auth only means that the username and password are sent in the Authorization header
the real question is: what happens after a successful login request (edited)
and that's where tokens come into play
How will you plan to persist the users login state with that method?
Are you going to require the the user to login for every request they make?

How do you handle persistence of login state in that case?
I’m currently using a bearer token for mine. I save that token to the Keychain then save the expiration to UserDefaults
token on the keychain

So the tokens get into play to keep the user logged in at all times?

That's the main purpose. since only the server can generate valid tokens (where validity can be a mathematical guarantee in case of JWTs, or a simply physical guarantee as the token has to exist in the database), if a client presents a valid token, the server trusts that the client is actually acting on behalf of the user
the user's password is important but should not be kept around neither the client nor the server for longer than absolutely necessary
which is why the password is only entered on the login screen, sent to the server in some form*, then forgotten as the server exchanges that to a token that it will trust for future requests
*there are algorithms like SRP where the password does not even have to leave the client, so it cannot be intercepted, but let's leave that off the table
trust is a key issue
you can't just let a client tell the server that "hey I'm sofotatos, show me all the chat messages" without proving it

--------
To remove the current database with: "docker rm -f postgres" in the terminal

Let's take our app to the next stage and introduce authentication into our app, so we know who is buying products

Authentication is about verifying who a user is, usually by using some sort of token or username and password

To start with, we will introduce a password for our user and talk about how to store passwords 

Then we will introduce a Token model to our application to enable us to authenticate users easily in the app

We will go over 3 different authentication methods:

- Basic Authentication (by sending the username and password in the request header) 
- Token Authentication (by sending a token to the application)
- Session Authentication (with a cookie for the website)

In order to secure our application we need to be able to identify our users. The most common way to do this, is through a username or password combination

Let's first talk about password security:

To make sure our app is as secure as possible, we must not store our passwords in plain text. So we must make sure if someone gets access to our password it can't be easily read. This means that we must store the passwords in a secure format. And one of the most common and secure ways of doing this is by usying the Bcrypt algorithm

Because we now have a password. When we create users through the API, we need to tweak things so that we hash the password, before we save it

Let's open up UsersController and inside createHandler, we can hash the user's password with Bcrypt before saving:
user.password = try Bcrypt.hash(user.password)

The final thing we have to do, is to change the public view of a user, Currently if we request all of the users or a single user, we will return their password hashed, which isn't something we particularly want to do, even if it is hashed

What we can do is create a new Model that looks like a user just with the missing password

So inside User, create a new struct called public that conforms to content and add all the properties apart from password

next create some functions that convert a user into a public user. The first will be for normal user


For the authentication part we will introduce a password and talk about how to store that password then we will introduce a token model to our application to enable you to authenticate users easily in the application

In order to secure our application, we need to be able to identify our users. The most common way to do this is through a username or password combination. But first we need to know about password security:

We must not store the passwords in plain text, so we must make sure that if someone does get access to the passwords, they can't be easily read. This means that you must store them in a secure format and one of the most common and secure ways to do that is by using the Bcrypt algorithm. Bcrypt is a slow hashing algorithm that can also apply randomness to each password. So it makes it difficult to bruteforce and we will be using this to secure the passwords

So let's get started:

In Xcode lets go to the User model and add a new property password which will be a String and add it to the initializer. Annotate the property with the field property wrapper and set the key to password

Because we now have a password, when we create users through the API, we need to tweak things so that we hash the password before we save it

Lets open up UsersController and inside createHandler we can hash the user's password with Bcrypt before saving it in the database

Currently if we request all of the users or single user, we will return their password hash, which isn't something we particullary want to do, even if it is hashed

What we can do is create a new Model that looks like a user just with the missing password

So inside user, lets create a new type called public that conforms to content

Now that it is all set, lets create a user with rested at:

POST http://127.0.0.1:8080/api/users

We notice that we don't get a password returned in the response. The same is true when we request all of the 
users like so:

GET http://127.0.0.1:8080/api/users

A common way of authenticating users is to use a token, so we need a model for that

lets create a new file called Token.swift in /models

Lets import vapor and fluent and create a token class and make it conform to model and content

then add an ID as UUID and a token property as a string

The first app of the application that we want to add authentication to is the API, Specifically we want to ensure that we know the user that is creating the car

We have succesfully completed authentication and we will now test it in postman:

POST http://127.0.0.1:8080/api/users/login

click the authorization button and enter the username and password we created for our user nikos

If we send the request, we should see the token in the response

Copy the token value and then click the authorization button and remove the values we entered before

Delete the username and password from the authorization and enter:

POST http://127.0.0.1:8080/api/cars

fill in the car info and send it, as we see we get back an 401 Unauthorized response!

 






















































