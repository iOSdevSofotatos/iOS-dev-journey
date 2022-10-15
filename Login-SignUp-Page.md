
So I use ASP.NET?, meaning I create a project on Xcode in swiftUI
Then I also create a local SQL server (this is where you can also spin up a paid online server with microsoft, amazon, etc.) and create the tables I need for it (like username, password fields to store user info)
Then i go back to my ASP.NET project, create an API Controller, and add the url routes and functions to get information from the database
typically i will use a class like ‘UserDAO’ (meaning User Data Access Object, which is also custom made and the most complex part if you aren’t familiar with SQL) to access the SQL database within my API Controller


Vapor is a framework developed in Swift language that provides useful tools to developers who want to create their own Server Side Apps. When we talk about Server Side Apps we don’t mean the typical Xcode files that let’s you operate with the different views, models, classes and so on. Instead, we are talking about the development of back-end apps that offer different ways to interact with servers. Imagine to have the need of a specific service that let’s you order food by means of your iOS app: in case you want to employ your own server database and APIs you would need to tell your Server Side App how to behave in case any order request is performed by means of your client.

-API: an Application Programming Interface is a group of different operations that let’s you manage a certain task. But, practically, what does this mean? Let’s say that you want to perform an order with one of your iOS apps. Once you make it you will send a request to a certain server that will have to behave in a specific way. This way is defined by APIs that are able to intermediate.

-Client: when we talk about a client we mean any end device with which the user interacts. This means that your device is the client that, thanks to the methods declared in the used app, is able to send specific requests managed by the APIs.

Azure data studio is basically a gui that we will be using to read from our database. So we can read the data in our database, without needing the API and without needing the iOS app.

we are going to create the backend for an iOS app that will then talk to the database

vapor new e-shop to create a new vapor project in the terminal
