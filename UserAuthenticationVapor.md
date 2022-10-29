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












































































