In the previous tutorial, we learned how to create an acronym and get all of the acronyms, but there
are a fiew more CRUD operations, that we want to be able to do, that will be useful. Getting individual acronyms, updating acronyms and deleting acronyms

First we will see how to get a single acronym

In Xcode in the "acronyms controller", we are going to create another handler, get handler, which will return a single acronym in the future:

func getHandler(_ req: Request) throws -> EventLoopFuture<Acronym> {
        
}

Vapor makes this really simple to implement. Perform a find operation on acronym
and pass in the parameter called acronym ID:
  
func getHandler(_ req: Request) throws -> EventLoopFuture<Acronym> {
    Acronym.find(req.parameters.get("acronymID"), on: req.db)
}

Finally, unwrap the optional future and return a 404 if you don't find an acronym:

func getHandler(_ req: Request) throws -> EventLoopFuture<Acronym> {
    Acronym.find(req.parameters.get("acronymID"), on: req.db).unwrap(or: Abort(.notFound))
}

Then register the route handler in boot with the following:

func boot(routes: Vapor.RoutesBuilder) throws {
    let acronymsRoutes = routes.grouped("api", "acronyms")
    acronymsRoutes.get(use: getAllHandler)
    acronymsRoutes.post(use: createHandler)
    acronymsRoutes.get(":acronymID", use: getHandler)
}


The dynamic parameter passed to the get call here, matches the parameter that you request here (acronymsRoutes.get(":acronymID", use: getHandler))

Deleting something is just as easy. You will create a new route delete handler and you are going to return a future HTTP status


















































