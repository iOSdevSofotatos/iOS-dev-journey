
Uses Fluent to interact with and store that information in a PostgreSQL database
and running that database on Docker

First, create a new directory in your home directory or somewhere sensible to work on your Vapor projects. 
For example, enter the following commands in Terminal:

mkdir vapor
cd vapor

This creates a new directory in your home folder called vapor and navigates you there. Next, create your project with:

vapor new vapor-project-name

The toolbox then asks if you’d like to use Fluent. For now, type n followed by Enter. You’ll learn about Fluent in a future tutorial. 
The toolbox then generates your project for you

To build and start your app, run:

cd vapor-project-name
open Package.swift

This builds and runs the app. The first time you run it, it can take some time since it must fetch all the dependencies. 
The template has a predefined route, so open your browser and visit http://localhost:8080/hello and see the response!

The template contains everything you need to set up your app and you shouldn’t need to change main.swift
or the Run module. Your code lives in App or any other modules you define

# Routing

## Creating Your Own Routes

Now that you’ve made your first app, it’s time to see how easy it is to add new routes with Vapor

After you open the vapor project, open routes.swift in Sources/App. You’ll see the route you visited above. To create another route, add the following after the app.get("hello") closure:

## What is routing

Routing is the process of finding the appropriate request handler for an incoming request

To understand how routing works in Vapor, you should first understand a few basics about HTTP requests. Take a look at the following example request:

GET /hello/vapor HTTP/1.1
host: vapor.codes
content-length: 0

This is a simple GET HTTP request to the URL /hello/vapor. This is the kind of HTTP request your browser would make if you pointed it to the following URL:

http://vapor.codes/hello/vapor

The first part of the request is the HTTP method. GET is the most common HTTP method, but there are several you will use often. These HTTP methods are often associated with CRUD semantics.

Methods
GET	    Read
POST	  Create
PATCH	  Update
DELETE	Delete

## Request Path

Right after the HTTP method is the request's URI. This consists of a path starting with / and an optional query string after ?. The *HTTP method* and *path* are what Vapor uses to route requests (για να κάνει αιτήματα διαδρομής)

After the URI is the HTTP version followed by zero or more headers and finally a body. Since this is a GET request, it does not have a body

By according to HTTP Standard, GET should not have request body because it only do query strings 
(γιατί κάνει μόνο ερωτήματα)

Both GET and POST method is used to transfer data from client to server in HTTP protocol but Main difference between POST and GET method is that GET carries request parameter appended in URL string while POST carries request parameter in message body which makes it more secure way of transferring data from client to server

## Router Methods

Let's take a look at how this request could be handled in Vapor.

app.get("hello", "vapor") { req in 
    return "Hello, vapor!"
}

All of the common HTTP methods are available as methods on Application. They accept one or more string arguments that represent the request's path separated by /

Note that you could also write this using "on" followed by the method

app.on(.GET, "hello", "vapor") { ... }

With this route registered, the example HTTP request from above will result in the following HTTP response
but first lets explain what HTTP response is

### HTTP response: 
An HTTP response is made by a server to a client. The aim of the response is to provide the client with the resource it requested, or inform the client that the action it requested has been carried out; or else to inform the client that an error occurred in processing its request

the example HTTP request from above will result in the following HTTP response:

HTTP/1.1 200 OK
content-length: 13
content-type: text/plain; charset=utf-8

Hello, vapor!

## Route Parameters

Now that we've successfully routed a request (δρομολογήσαμε ένα αίτημα) based on the HTTP method and path, let's try making the path dynamic. Notice that the name "vapor" is hardcoded in both the path and the response. Let's make this dynamic so that you can visit /hello/<any name> and get a response

app.get("hello", ":name") { req -> String in
    let name = req.parameters.get("name")!
    return "Hello, \(name)!"
}
  
By using a path component prefixed with :, we indicate to the router that this is a dynamic component. Any string supplied here will now match this route. We can then use req.parameters to access the value of the string

If you run the example request again, you'll still get a response that says hello to vapor. However, you can now include any name after /hello/ and see it included in the response. Let's try /hello/swift:

REQUEST
GET /hello/swift HTTP/1.1
content-length: 0
  
RESPONSE
HTTP/1.1 200 OK
content-length: 13
content-type: text/plain; charset=utf-8

Hello, swift!

Now that you understand the basics, check out each section to learn more about parameters, groups, and more

## Routes
A route specifies a request handler for a given HTTP method and URI path. It can also store additional metadata

## Methods
Routes can be registered directly to your Application using various HTTP method helpers.

// responds to GET /foo/bar/baz
app.get("foo", "bar", "baz") { req in
    ...
}

Route handlers support returning anything that is "ResponseEncodable". This includes "Content", an "async" closure, and any "EventLoopFutures" where the future value is "ResponseEncodable"

ResponseEncodable ??
    
You can specify the return type of a route using -> T before in. This can be useful in situations where the compiler cannot determine the return type
    
app.get("foo") { req -> String in
    return "bar"
}

These are the supported route helper methods:

- get
- post
- patch
- put
- delete
    
In addition to the HTTP method helpers, there is an on function that accepts HTTP method as an input parameter

// responds to OPTIONS /foo/bar/baz
app.on(.OPTIONS, "foo", "bar", "baz") { req in
    ...
}

## What is the HTTP OPTIONS request used for?
The HTTP OPTIONS method is used to describe communication options for the target resource. Browsers send an HTTP OPTIONS request to find out the supported HTTP methods and other options supported for the target resource before sending the actual request. HTTP OPTIONS requests allow clients to obtain parameters and requirements for specific resources and server capabilities without taking action on the resource or requesting the resource
    
## Path Component

Each route registration method accepts a variadic list of PathComponent. This type is expressible by string literal and has four cases:

- Constant (foo)
- Parameter (:foo)
- Anything (*)
- Catchall (**)
  
### Constant
This is a static route component. Only requests with an exactly matching string at this position will be permitted
    
// responds to GET /foo/bar/baz
app.get("foo", "bar", "baz") { req in
    ...
}    
    
### Parameter   
This is a dynamic route component. Any string at this position will be allowed. A parameter path component is specified with a : prefix. The string following the : will be used as the parameter's name. You can use the name to later fetch the parameters value from the request    
    
 // responds to GET /foo/bar/baz
// responds to GET /foo/qux/baz
// ...
app.get("foo", ":bar", "baz") { req in
    ...
}   
    
### Anything    
This is very similar to parameter except the value is discarded. This path component is specified as just *.

// responds to GET /foo/bar/baz
// responds to GET /foo/qux/baz
// ...
app.get("foo", "*", "baz") { req in
    ...
}
    
### Catchall   
This is a dynamic route component that matches one or more components. It is specified using just **. Any string at this position or later positions will be matched in the request
    
// responds to GET /foo/bar
// responds to GET /foo/bar/baz
// ...
app.get("foo", "**") { req in 
    ...
}
    
## Parameters    
When using a parameter path component (prefixed with :), the value of the URI at that position will be stored in req.parameters. You can use the name of the path component to access the value
    
// responds to GET /hello/foo
// responds to GET /hello/bar
// ...
app.get("hello", ":name") { req -> String in
    let name = req.parameters.get("name")!
    return "Hello, \(name)!"
}   
    
req.parameters.get also supports casting the parameter to LosslessStringConvertible types automatically    

// responds to GET /number/42
// responds to GET /number/1337
// ...
app.get("number", ":x") { req -> String in 
    guard let int = req.parameters.get("x", as: Int.self) else {
        throw Abort(.badRequest)
    }
    return "\(int) is a great number"
}    
    
The values of the URI matched by Catchall (**) will be stored in req.parameters as [String] (array of strings). You can use req.parameters.getCatchall to access those components   
    
// responds to GET /hello/foo
// responds to GET /hello/foo/bar
// ...
app.get("hello", "**") { req -> String in
    let name = req.parameters.getCatchall().joined(separator: " ")
    return "Hello, \(name)!"
}  
    
## Body Streaming
When registering a route using the on method, you can specify how the request body should be handled. By default, request bodies are collected into memory before calling your handler. This is useful since it allows for request content decoding to be synchronous (συγχρονισμένα) even though your application reads incoming requests asynchronously (ασύγχρονα)  
    
By default, Vapor will limit streaming body collection to 16KB in size. You can configure this using app.routes
 
// Increases the streaming body collection limit to 500kb
app.routes.defaultMaxBodySize = "500kb"   
    
If a streaming body being collected exceeds the configured limit, a 413 Payload Too Large error will be thrown
    
To configure request body collection strategy for an individual route, use the body parameter
    
// Collects streaming bodies (up to 1mb in size) before calling this route.
app.on(.POST, "listings", body: .collect(maxSize: "1mb")) { req in
    // Handle request. 
}    
    
If a maxSize is passed to collect, it will override the application's default for that route. To use the application's default, omit (παραλείψτε) the maxSize argument  
    
For large requests, like file uploads, collecting the request body in a buffer can potentially strain your system memory. To prevent the request body from being collected, use the stream strategy
    
// Request body will not be collected into a buffer.
app.on(.POST, "upload", body: .stream) { req in
    ...
}
    
When the request body is streamed, req.body.data will be nil. You must use req.body.drain to handle each chunk as it is sent to your route
    
## Case Insensitive Routing  
Default behavior for routing is both case-sensitive (με διάκριση πεζών-κεφαλαίων) and case-preserving. Constant path components can alternately be handled in a case-insensitive and case-preserving manner for the purposes of routing; to enable this behavior, configure prior to application startup:    
    
app.routes.caseInsensitive = true    
    
No changes are made to the originating request; route handlers will receive the request path components without modification   
        
## Viewing Routes    
You can access your application's routes by making the Routes service or using app.routes.all

print(app.routes.all) // [Route]    

## Metadata
All route registration methods return the created Route. This allows you to add metadata to the route's userInfo dictionary. There are some default methods available, like adding a description
    
app.get("hello", ":name") { req in
    ...
}.description("says hello")
    
## Route Groups
Route grouping allows you to create a set of routes with a path prefix or specific middleware. Grouping supports a builder and closure based syntax.

All grouping methods return a RouteBuilder meaning you can infinitely mix, match, and nest your groups with other route building methods    
       
## Path Prefix
Path prefixing route groups allow you to prepend (προπαρασκευάζω) one or more path components to a group of route
    
let users = app.grouped("users")
// GET /users
users.get { req in
    ...
}
// POST /users
users.post { req in
    ...
}
// GET /users/:id
users.get(":id") { req in
    let id = req.parameters.get("id")!
    ...
} 
    
Any path component you can pass into methods like get or post can be passed into grouped. There is an alternative, closure-based syntax as well    
    
app.group("users") { users in
    // GET /users
    users.get { req in
        ...
    }
    // POST /users
    users.post { req in
        ...
    }
    // GET /users/:id
    users.get(":id") { req in
        let id = req.parameters.get("id")!
        ...
    }
}    
  
Nesting path prefixing route groups allows you to concisely define CRUD APIs.

app.group("users") { users in
    // GET /users
    users.get { ... }
    // POST /users
    users.post { ... }

    users.group(":id") { user in
        // GET /users/:id
        user.get { ... }
        // PATCH /users/:id
        user.patch { ... }
        // PUT /users/:id
        user.put { ... }
    }
}    
    
## Middleware    
Middleware is a logic chain between the client and a Vapor route handler. It allows you to perform operations on incoming requests before they get to the route handler and on outgoing responses before they go to the client
    
In addition to prefixing path components, you can also add middleware to route groups
    
app.get("fast-thing") { req in
    ...
}
app.group(RateLimitMiddleware(requestsPerMinute: 5)) { rateLimited in
    rateLimited.get("slow-thing") { req in
        ...
    }
}

This is especially useful for protecting subsets of your routes with different authentication middleware

app.post("login") { ... }
let auth = app.grouped(AuthMiddleware())
auth.get("dashboard") { ... }
auth.get("logout") { ... }  

## Redirections      
Redirects are useful in a number of scenarios, such as forwarding old locations to new ones for SEO, redirecting an unauthenticated user to the login page or maintain backwards compatibility with the new version of your API.

To redirect a request, use:

req.redirect(to: "/some/new/path")    
    
You can also specify the type of redirect, for example to redirect a page permanently (so that your SEO is updated correctly) use:
    
req.redirect(to: "/some/new/path", type: .permanent)    
    
The different RedirectTypes are:

- permanent - returns a 301 Permanent redirect
- normal - returns a 303 see other redirect. This is the default by Vapor and tells the client to follow the redirect with a GET request
- temporary - returns a 307 Temporary redirect. This tells the client to preserve the HTTP method used in the request    
  
# Content    
Vapor's content API allows you to easily encode / decode Codable structs to / from HTTP messages. JSON encoding is used by default with out-of-the-box support for URL-Encoded Form and Multipart. The API is also configurable, allowing for you to add, modify, or replace encoding strategies for certain HTTP content types    
    
## Overview    
To understand how Vapor's content API works, you should first understand a few basics about HTTP messages. Take a look at the following example request   
    
POST /greeting HTTP/1.1
content-type: application/json
content-length: 18

{"hello": "world"}    
    
This request indicates that it contains JSON-encoded data using the content-type header and application/json media type. As promised, some JSON data follows after the headers in the body    
    
## Content Struct    
The first step to decoding this HTTP message is creating a Codable type that matches the expected structure

 struct Greeting: Content {
    var hello: String
}   
    
Conforming the type to Content will automatically add conformance to Codable alongside additional utilities for working with the content API   
    
Once you have the content structure, you can decode it from the incoming request using req.content    
    
app.post("greeting") { req in 
    let greeting = try req.content.decode(Greeting.self)
    print(greeting.hello) // "world"
    return HTTPStatus.ok
}    
    
The decode method uses the request's content type to find an appropriate decoder. If there is no decoder found, or the request does not contain the content type header, a 415 error will be thrown.

That means that this route automatically accepts all of the other supported content types, such as url-encoded form:    
    
POST /greeting HTTP/1.1
content-type: application/x-www-form-urlencoded
content-length: 11

hello=world    
    
In the case of file uploads, your content property must be of type "Data"

struct Profile: Content {
    var name: String
    var email: String
    var image: Data
}    
    
Below are the media types the content API supports by default:

- JSON	
- Multipart	
- URL-Encoded 
- Plaintext	
- HTML	
    
## Query    
Vapor's Content APIs support handling URL encoded data in the URL's query string    
    
### Decoding   
To understand how decoding a URL query string works, take a look at the following example request

GET /hello?name=Vapor HTTP/1.1
content-length: 0   
    
Just like the APIs for handling HTTP message body content, the first step for parsing (αναλύοντας) URL query strings is to create a struct that matches the expected structure    
    
struct Hello: Content {
    var name: String?
}    
    
Note that name is an optional String since URL query strings should always be optional. If you want to require a parameter, use a route parameter instead    
    
I used the async APIs on URLSession to send HTTP requests from the frontend, and I used Vapor and the MongoDB driver’s async APIs to handle requests on the backend
    
QUESTION    
sorry if this sounds dumb I still try to understand the connection between the frontend and the backend, lets say I want to build an e-shop with swiftUI for the frontend and vapor to handle the backend. When my user first open the app, he will see the login section, after he enters his username and password and presses the login button, a GET request will be sent to the server, it will check if the user and password match and will send back a response. My question is,  are both the request and response handled by vapor?

ANSWER
yes

but a GET request is a bad choice for a login request 
    
even if done over HTTPS, there's a good chance that it might get logged, with all the query parameters intact -> the users' credentials in it    
    
What do you mean how I would approach it? There's not much room for creativity, you need a POST request with the credentials (username and password for example), and in return you send a token back that the app can store on the keychain, and include with later requests   
    
The token returned is used within a Bearer Authorization header and sent with each request after login. it is unique to the user and should change with every login request (and timeout server side). It is how the backend knows who the user is without requiring them send their credentials with every request.

For the 4 main request types, there is a general rule of thumb (at least this is how I learned them)

GET: Read only requests
POST: CREATING a record or something (Login creating a session for example, adding items to a shopping cart, purchase, etc)
PUT: UPDATING an existing record (password change, update profile)
DELETE: ... Deleting a record.

Things to keep in mind when designing the App/Server interactions.    
    
    
    
    
    
    
    
    
    

    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
The application contains a REST API which the iOS frontend uses to interact with it via HTTP requests
https://github.com/mongodb/mongo-swift-driver/blob/main/Examples/FullStackSwiftExample/Backend/README.md
    
    
We use Azure Data Studio (GUI - Graphical user interface) to read the data in our database without needing the API and the iOS app
 
## Vapor file structure    
    
The Sources folder is where all of our code is going to live    
    
Controllers folder - by default it gives us a todo controller (a todo application) so here is where we can group logic inside of controllers rather than putting it all on a single file   
    
Migrations are used with the database. So we will be making database migrations, and this is things like preparing our database, meaning creating new tables in our database
    
Model is what models the data that is in our database and we can use these within our application
  
Configure function has just one function in here and it configures (διαμορφώνει) the database we will be using, it adds our Migration and it runs the routes function and then routes function is where all of our routes exist. So different endpoints that will be requesting from with our iOS application
  
Inside the Run folder, this contains the code that we need to get our application up and running. So you can see it calls the "Configure function"
   
Inside of tests by default, Vapor gives us a unit test that we can use. So the unit test right here is starting the application, we are going to the route/hello and then we want to assert (διεκδικώ) that this is okay and it gives us back "hello world"
    
We have a docker-compose file, this is used to create a multi Docker application. This configures our api to run and for the database to run in docker file. This is what is used to actually create a Docker image
    
Package.resolved - These are the exact versions of the packages that are inside of our vapor project that are defined in Package.swift (So if somebody were to download and clone your application from github, they will use these exact versions) 

# Lesson 2
    
We will see how to use Vapor to create tables, use migrations, and how to read and write data to the database 
    
We will write our own routes inside of the controller rather than writing them inside the routes file, because if you have a big app it will get crowded

Migrations are used when creating our database, so when you think we have version control within our project, when we type new lines and change the Xcode, we use GIT to tell us what have we changed? And we can revert back to certain points in time for migrations with the database. For a database we use migrations to do that, as opposed to using GIT, so we first need to import fluent ( fluent is an object relational mapper) and we will use that for migration. So the migration will then track the differences between our database for things that we add. So first lets create a struct, The two functions (prepare and revert) are the changes we want to make and then revert ( what we do if want to revert those changes)
    
Tables have rows and collumns, rows are all of our data and columns are the different properties of our data
  
// This is our Migration that we will make for our Users table
// How do we represent the data that is in this table?
// We have to create a Model for that   
 
Model folder - User.swift
import Fluent
import Vapor

final class User: Model, Content {
    
}

We can think of it like a normal class that we would make within an iOS project.
This is how we want to represent our data. But because we are using fluent, this is how we are going to query the data from the database
    
    
What we need to do is to run our database first because currently it is not running. What vapor does, is it kind of just only runs the application. the API sort of side, it does not really run the database 
    
Type in terminal to start the database: docker compose up db 
    
What this does is it is creating our database for us within a Docker container and it is doing that by using our Docker file here in docker-compose.yml  
    
With Azure Data Studio, we can look at our data in the database directly without using the API. But to do that first we need to add a connection, so we open azure data studio and fill in the inputs required 
    
Lets create a new route that allows us to add data, because the routes we have so far are just allowing us to get data  
    
    
    
- HTTP: Learn the basics of how to make requests to and from servers.
- Fluent: Learn how to use Fluent to save and manage your models in databases.
- Controllers: Learn how to use controllers to route your requests and responses.
- Leaf: Learn how Vapor’s Leaf module and its templating language allow you to build dynamic web sites directly.
- Middleware: Learn how built-in Vapor modules can assist with common tasks such as validating users, settings required response headers, serving static files and more.
One thing you can count on: After reading this book, you’ll be prepared to write your own server-side applications using Vapor and, of course, Swift.
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
=========================================================================================================================

Start by looking at the classes defined in the Models directory:
User: Represents a user entry in your server’s database.
Dinner: Represents a scheduled dinner.
DinnerInviteePivot: Links dinners and invited users.

When looking into these class properties, notice how Vapor 4 uses property wrappers, a new feature in Swift 5.1. The @ID, @Field and @Timestamp wrappers, among others, play an essential role for Fluent, Vapor’s database framework

Now, open the files in the Controllers folder to get a sense of how Vapor 4 handles routing

Next, open the Migrations directory and check how you add a new table and its columns to the database. Also, pay attention to how each property has additional attributes, like constraints or references to other tables








































Heroku when it is time, until then keep it local




Note: For the sake of simplicity, this tutorial uses an on-disk SQLite database. In later stages of development, and certainly in production, you should use a scalable database running on a dedicated server. Good choices include PostgreSQL and MySQL.























