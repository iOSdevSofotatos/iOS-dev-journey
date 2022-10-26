In the first section about some of the basics of Vapor

How to send and receive data with JSON and how routes work

In this session of the course, we will build a real API, how to organize our routes with Controllers, use Fluent to interact with a database, Learn Fluent relationships and queries and integrate with PostgreSQL

We will look into controllers and see how they can be used to organize your code to keep applications 
maintanable and clean

Before we begin, lets take a quick look at Fluent

Fluent is an ORM, or object relational mapping tool created by the vapor team
It is designed to take away alot of work of dealing with a database by creating easy-to-use database integrations

### Think of Fluent like an abstraction layer between your Vapor app and your database. Using an ORM such as Fluent has a number of benefits:

- No need to remember SQL (don't have to worry about how to actually use the database. For example: You don't need to remember the SQL query to update a row in a database)
So instead of having to write an insert into table sql query, you can simply call model.save

- It makes working with a database a lot easier in a language such as Swift, Swift is a strongly type, sttically typed language. Things inside a database generally aren't. Converting a row from a database into something Swift can use is painful, but fluent will make it really easy

- Finally, official Fluent integrations () are available for PostgreSQL, MySQL, MongoDB and SQLite. This means a lot of what you learn using Fluent, can be applied to other database types you may want to use in the future. Each of the integrations take advantage of the power of the specific database type. For instance, with PostgreSQL you get native geometric support

## Async 

The introduction of Async is one of the biggest changes in Vapor 4 and can be a very confusing aspect for those getting started. Moving to an asynchronous architecture has provided huge performance boosts, to the point where Vapor is now one of the fastest frameworks around and it works on simillar way to asynchronous aspects of iOS

Vapor 4 has a set number of threads that we can use (to handle requests)

In Vapor 2 when you get given a request to handle, you have to return a response and you can't give it nothing and say, "Wait there until I'm ready" Once you hand the response back, it is gone

What you want to do when you give it to the database, is wrap it up in some sort of promise to say, "When you have the result, do this". And that is exactly how Async works in Vapor

Most functions will return what's called a future, which is a result that we returned at some point in the future and you can chain and add to these

So to demonstrate this, I'm now a single threaded asynchronous worker and i get given a request. In this request, I need to get something from the database. So what I can do is give the request a promise, that I will return the user when I get it from the database. I can now put this request down, while I'm waiting for the database to respond and handle another request that just needs a String, so I can return that String immediately. If I get a third request which needs something from an external API, I can wrap up in a promise and put the request down again. The API call then returns which I can handle and return the response. Finally the database call returns, I can pick that up, execute my promise, and return the response

In the above example I've been able to handle 3 requests in the time that I would only have been able to handle one in a synchronous world. That is the reason for Async and how it works

Vapor is built on top of SwiftNIO, Apple's non-blocking event-driven IO library. This provides the asynchronous architecture that Vapor uses and manages all the handling of threads and event loops

Vapor provides a number of syntactic sugar helpers for working with asynchonous code, the two most common you will come across are flatMap and map 

## flatMap
### step 1
flatMap takes one future and returns another future in a closure for the promise. For example: in this bit of code, I have a user manager that has a function to get all the users. I want to change and then save the first user so I can call: return userManager.getAllUsers(on: req.db)

### step 2
and flapMap the result to wait for all the users to be retrieved from the database:
return userManager.getAllUsers(on: req.db).flatMap { users in

}

### step3
Now that I have all the users I can update the first user and save and return the result
return userManager.getAllUsers(on: req.db).flatMap { users in
    let user = users[0]
    user.name = "Alice"
    return user.save(on: req.db)
}

## map
The other variant of this is map. Map takes a future and returns a non-future object in the promise closure

The key difeerence is what you return in the closure. If you are returning a future, use flatMap, if you are returning a non-future, use map

Going back to our example, if we wanted to return an HTTP status code instead of user, we could use map to #wait# for the result, then save and return a status: 
return userManager.getAllUsers(on: req.db).flatMap { users in
    let user = users[0]
    user.name = "Bob"
    return user.save(on: req.db).map { user in
        return HTTPStatus.noContent
    }
}

Since the closure of the map call returns a non-future (the HTTP status code), we used map instead of flatMap

Both flatMap and map are waiting because they fetch the database for data

You can also chain the futures instead of nesting them so you could rewrite the example to this:
return userManager.getAllUsers(on: req.db).flatMap { users in
    let user = users[0]
    user.name = "Bob"
    return user.save(on: req.db)
}.map {
    return HTTPStatus.noContent
}

Another common function to use is transform. This is used when you don't care about the result of a future, only that it completed succesfully. Going back to the example, you don't care about the saved user so you can remove the map call and use transform instead:
return userManager.getAllUsers(on: req.db).flatMap { users in
    let user = users[0]
    user.name = "Bob"
    return user.save(on: req.db).transform(to: .noContent)
}

There are ocasions, where you need to wait for multiple futures, instead of nesting our futures, you can use one of two methods:

If the futures are all of the same type such as saving multiple users, we can add them to array and flatten them. For instance, if we wanted to save two users, we could create our two users and then use an array of futures to store the results. We could then flatten the array, which waits for the futures to return:
func createUsers(on req: Request) -> EventLoopFuture<HTTPStatus> {
    let user1 = User(name: "Alice")
    let user2 = User(name: "Bob")
  
    var userSaveResults = [EventLoopFuture<void>]()
    userSaveResults.append(user1.save(on: req.db))
    userSaveResults.append(user2.save(on: req.db))
  
    return userSaveResults.flatten(on: req.eventLoop).map {
        return .ok
    }
}

### step 1
Finally if you have multiple different futures, you can "and" them together. For instance, if you wanted to get all of the users and all of the posts, both of which return futures, you could write this:

func getAllUsersAndPosts(on req: Request) -> EventLoopFuture<HTTPStatus> {
    return userManager.getAllUsers(on: req.db).and(postManager.getAllPosts(on: req.db)).flatMap { 
        users, posts in
        
    }
}

### step 2
The above returns the resolved futures in the closure, however as you are using flatMap, you need to return the future HTTP status. You can do this by wrapping the status in req.eventLoop.future:
func getAllUsersAndPosts(on req: Request) -> EventLoopFuture<HTTPStatus> {
    return userManager.getAllUsers(on: req.db).and(postManager.getAllPosts(on: req.db)).flatMap { 
        users, posts in
        return req.eventLoop.future(.ok)
    }
}

### use .map instead of .flatMap
We can of course use the same functionlity with map, so we could change our code to avoid having to wrap the status code in the future at all:
func getAllUsersAndPosts(on req: Request) -> EventLoopFuture<HTTPStatus> {
    return userManager.getAllUsers(on: req.db).and(postManager.getAllPosts(on: req.db)).map { 
        users, posts in
        return .ok
    }
}

Note that if we "and" more than two futures together, we will get a nested tuple back, which we will need to handle. We will see an example of this in one of the sessions



































