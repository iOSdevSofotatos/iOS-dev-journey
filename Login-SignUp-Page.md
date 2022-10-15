
So I use ASP.NET?, meaning I create a project on Xcode in swiftUI
Then I also create a local SQL server (this is where you can also spin up a paid online server with microsoft, amazon, etc.) and create the tables I need for it (like username, password fields to store user info)
Then i go back to my ASP.NET project, create an API Controller, and add the url routes and functions to get information from the database
typically i will use a class like ‘UserDAO’ (meaning User Data Access Object, which is also custom made and the most complex part if you aren’t familiar with SQL) to access the SQL database within my API Controller


Vapor is a framework developed in Swift language that provides useful tools to developers who want to create their own Server Side Apps. When we talk about Server Side Apps we don’t mean the typical Xcode files that let’s you operate with the different views, models, classes and so on. Instead, we are talking about the development of back-end apps that offer different ways to interact with servers. Imagine to have the need of a specific service that let’s you order food by means of your iOS app: in case you want to employ your own server database and APIs you would need to tell your Server Side App how to behave in case any order request is performed by means of your client.

-API: an Application Programming Interface is a group of different operations that let’s you manage a certain task. But, practically, what does this mean? Let’s say that you want to perform an order with one of your iOS apps. Once you make it you will send a request to a certain server that will have to behave in a specific way. This way is defined by APIs that are able to intermediate.

-Client: when we talk about a client we mean any end device with which the user interacts. This means that your device is the client that, thanks to the methods declared in the used app, is able to send specific requests managed by the APIs.

Azure data studio is basically a gui that we will be using to read from our database. So we can read the data in our database, without needing the API and without needing the iOS app.

vapor new e-shop to create a new vapor project in the terminal

we are going to create the backend for an iOS app that will then talk to the database. So the four database operations are.. create, read, update and delete. So create means adding new data to the database, read is reading the data from the database, update is updating any data that is already in the database and then delete is to delete any data from the database.
.............................................................................................................................

when we write to the database we are only performing 3 of the 4 operations (create, update, delete) (check image below)

So we will be doing create, so that is creating some data on the iOS app, sending the data to our server to the vapor api and then that sends it and saves it to the database and then when we update and delete, that is again an operation coming from the iOS app telling the API to do something, then the API is taking that and doing something to the database

![Screenshot 2022-10-16 at 1 30 36 AM](https://user-images.githubusercontent.com/112722460/196010064-984e322e-3f46-49b3-bd65-8e2a5f569bba.jpg)


and then when we are going to read from the database, it starts with the iOS app asking for data, it goes to the api or the server that tells it, hey this is what data we need, and then it translates it and grabs that data from the database. So the database takes that data, it gives it back to our server and then sends it back to the iOS app

![Screenshot 2022-10-16 at 1 34 33 AM](https://user-images.githubusercontent.com/112722460/196010072-f84685da-4c33-4853-9c58-ec234c7e7bb7.jpg)

server is the api i think, different from database

So we had to download homebrew, which is a package manager that we had to use to download everything else and we had to download postman. So postman allows us to test the API without actually needing to create the iOS app first. And with the database we are going to be using azure data studio and that allow us to look at the data, directely in the database without having to use our API

![Screenshot 2022-10-16 at 1 41 16 AM](https://user-images.githubusercontent.com/112722460/196010203-a1bc81a2-59e2-4a04-b4f7-1a12f4daf217.jpg)

if we open postman and go to that adress that vapor is running px 127.0.0.1:8080

# inside vapor xcode

![Screenshot 2022-10-16 at 2 07 49 AM](https://user-images.githubusercontent.com/112722460/196010764-144a193c-3eb8-48b7-8bc9-df1bdc08da63.jpg)

## SOURCES -> APP -> CONTROLLERS
we can group logic inside of controllers rather than putting it all in a single file. 

## SOURCES -> APP -> Migrations
Migrations are used with the database. So we will be making what are called database migrations and this is things like preparing our database, meaning creating new tables in our database.

## SOURCES -> APP -> Models
We have our Model, so this is what models the data that is in our database and we can use these within our application

## SOURCES -> APP -> configure.swift
and we have the configure function, So this just has one function in here and it configures the database we will be using, it adds our migration and it runs the routes functiom

## SOURCES -> APP -> routes.swift
and then routes function is where all of our routes are so different endpoints that will be hit,will be requesting from with our iOS application, so we saw first the get route. So these are different endpoints that our API has. and then here is the todo controller, because controllers has their own routes

## SOURCES -> APP -> RUN -> main.swift
Inside the run folder, this contains the code that we need to get out application up and running. So you can see it calls the configure function that we defined right there

## SOURCES -> APP -> TESTS -> AppTests -> AppTests.swift
Inside of test by default, vapor gives us a unit test, that we can use. So the unit right here is starting the application , we are going to the route/hello and then we want to assert that this is okay and it gives us back Hello World

## SOURCES -> APP -> docker-compose.yml
We also have a docker composed file. This is used to create a multi docker application so this configures our API to run and for the database to run

## SOURCES -> APP -> DockerFile
in docker file this is what is used to actually create a Docker image 

## SOURCES -> APP -> Package.resolved
last is package.resolved. These are the exact versions of the packages that are inside of our project that are defined in package.swift. So if somebody where to download and clone your application from like github, they will use these exact versions, unless you go and ask to like upadate the packages

Part 1 completed.............................................................................................................

# Part 2

So in this part, we will see how to use vapor to create tables, use migrations and how to read and write data to the database

lets see how to use vapor to build our own API!












