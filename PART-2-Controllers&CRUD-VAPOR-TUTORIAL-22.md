Now that we have the model set up and integrated with fluent, we want to be able to actually save and retrieve things to do this. So will set up an API:
CRUD:
- Create
- Retrieve
- Update
- Delete

So will set up an API for acronyms that will perform CRUD operations for us

This API will be created by using a controller, controllers are simillar to those you will find on iOS. Generally, you use them to handle requests in vapor and deal with models, much like iOS. They are a great way to split up all of your routes. So anything to do with acronyms will be handled by the acronyms controller! 

Controllers are also a really good way of versioning your APIs. You can have one controller for version 1 and another controller for version 2

So just like the model, we are going to create a file for the acronym controller

So in Xcode in Sources/App/Controllers create a new file called AcronymsController.swift

Vapor has a handy protocol that can be used for controllers which is the route collection. So lets create our controller and make it conform to that protocol remembering to import Vapor

struct AcronymsController: RouteCollection {
    func boot(routes: Vapor.RoutesBuilder) throws {
        <#code#>
    }
}

RootCollection defines a boot function which is where you all register your routes. To make it easy and clear to use the API. You are gonna have everything behind /API/acronyms. So if you want to get all of the acronyms, you can send a get request to /API/acronyms. If you want to get an individual one, you can send a get request to /API/acronyms/the acronym ID

And then add support for other HTTP methods as needed. This is a pretty common pattern for rest APIs. Instead of having to write the /API/acronym path parameters for every route, you can put them all inside a single route group which makes for much nicer code. So create that route group as follows:

struct AcronymsController: RouteCollection {
    func boot(routes: Vapor.RoutesBuilder) throws {
        let acronymsRoutes = routes.grouped("api", "acronyms")
    }
}

The first thing you want to be able to do is to get all of the acronyms. So lets create a route handler to just do that:

Create a function called getAllHandler and the function signature will be very simillar to every route handler and Vapor:

func getAllHandler(_ req: Request) throws -> EventLoopFuture<[Acronym]> {
        
}

Note that the return type is EventLoopFuture array of acronyms

Future because you don't know when the database query will return

Inside the route handler, you can simply perform a fluent query to get all of the acronyms, and that is all we have to do

To use the route, just register it in the boot function as follows:

struct AcronymsController: RouteCollection {
    func boot(routes: Vapor.RoutesBuilder) throws {
        let acronymsRoutes = routes.grouped("api", "acronyms")
        // we register it in the boot function
        acronymsRoutes.get(use: getAllHandler)
    }
    
    func getAllHandler(_ req: Request) throws -> EventLoopFuture<[Acronym]> {
        // Gets all of the acronyms
        Acronym.query(on: req.db).all()
    }
}

Next we want to be able to create an acronym. After all an API where can only get acronyms, but can't create them propably is not very useful!

So we are going to create a new route handler called createHandler that will return the acronym, when it has been created. So the return type will be an EventLoopFuture acronym:

func createHandler(_ req: Request) throws -> EventLoopFuture<Acronym> {
        
}

Inside the handler we can get the acronym with request.content.decode and the type to decode:
  
func createHandler(_ req: Request) throws -> EventLoopFuture<Acronym> {
    let acronym = try req.content.decode(Acronym.self)
}

And then simply save it to the database:

func createHandler(_ req: Request) throws -> EventLoopFuture<Acronym> {
    let acronym = try req.content.decode(Acronym.self)
    return acronym.save(on: req.db)
}

.save return a future void so map the return future to the acronym:

func createHandler(_ req: Request) throws -> EventLoopFuture<Acronym> {
    let acronym = try req.content.decode(Acronym.self)
    return acronym.save(on: req.db).map{ acronym }
}

Since acronym is a class reference semantics means that the acronym you save and the acronym you return are the same instance
  
Now lets register the root in boot as a post request on /API/acronyms. Again this is a very common pattern for creating objects in rest APIs:
  
func boot(routes: Vapor.RoutesBuilder) throws {
    let acronymsRoutes = routes.grouped("api", "acronyms")
    acronymsRoutes.get(use: getAllHandler)
    acronymsRoutes.post(use: createHandler)
}

Finally before we can try it out, we need to register the controller. So open routes.swift and remove any code inside the routes function
  
Then create an instance of your controller inside routes.swift and register with the app:
func routes(_ app: Application) throws {
    // create an instance of our controller
    let acronymsController = AcronymsController()
    // register with the app
    try app.register(collection: acronymsController)
}
 
Now lets build and run our app, and check POSTMAN to see if our app works as intended!














  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
