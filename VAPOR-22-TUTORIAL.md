Routing is the process of finding the appropriate response to an incoming request

### An HTTP request may contain a number of pieces of information:

- Host (Host is a server to send the request to) and Path (The path tells the server what it is trying to do)
- Request Method (The Request will also contain the HTTP Method and the HTTP version (HTTP 1.1))
- Headers
- Maybe some data

The HTTP method tells the server what kind of operation it wants to perform

Other information can be send with a Request via Request Headers. These could include things like cookies,
which will tell the server who the user performing the request is

Or the Accept Header which tells the server what type of content the client can accept. This can be really usefull and Vapor uses this in its inbuilt error middleware. If there is an error, it will check this header to see what type of response to send. If the client accepts HTML then it is propably a browser, so it will return an HTML error page. If it is an iOS app, it will propably only accept json. So Vapor will return insights from Jason

The HTTP response will be returned, once the server has dealt with the request

First and foremost the request will contain a status code (200 OK)
This tells the client what the outcome of the request was

### Status Code

- 1xx Informational Response
- 2xx Success
- 3xx Redirection
- 4xx Client Error
- 5xx Server Error

### As well as a status code, you will usually get a response body

If you are requesting a webpage, then you will get the HTML contents in the response body. There will also be some headers set on the response. This will indicate things such as how long the response body is, or they could be used to set a cookie in the browser

### Accepting data from HTTP post requests that contain JSON in the request body
### and then how to return JSON

We will learn how to leverage the power of Swift and Codable to make this really easy with Vapor

Inside the route handler, you want to be able to get the data from the request

app.post("info") { req in

}

And Vapor makes this really easy by leveraging the power of Codable. Vapor makes heavy use of Codable to
conform JSON to and from the data with its Content protocol. So let define a new struct that will use this to convert your incoming data into something you can use

We will create a new struct called InfoData and make that conform to Content:

struct InfoData: Content {
    let name: String
}

We can then get Vapor to pass out InfoData from the request simply with:

app.post("info") { req in
    let data = try req.content.decode(InfoData.self)
}

Now that we have the request data, we can return the name, simply with:

app.post("info") { req in
    let data = try req.content.decode(InfoData.self)
    return "Hello \(data.name)"
}

And then give the compiler a hand by telling it that the closure returns a String 

app.post("info") { req -> String in
    let data = try req.content.decode(InfoData.self)
    return "Hello \(data.name)"
}

If we build and run the application, we can head back to POSTMAN and send the request we have set up,
we will see the response!

If you want to return a JSON response, which will be common for receiving a JSON request, this is similary easy: 

Head back to Xcode and you can define a new content struct that will be encoded back into JSON and sent back

This time you will see how you can nest different types 

First create a new struct called InfoResponse

struct InfoResponse: Content {
    
}

And in here we are going to return the request we were given
This is really simple all we need to do is to define a property request and that will be a type InfoData

struct InfoResponse: Content {
    let request: InfoData
}

If we now go back to the route handler, we can then simply return an instance of InfoResponse with the 
data from the request, also changing the return type of the closure as we now return a different type

app.post("info") { req -> InfoResponse in
    let data = try req.content.decode(InfoData.self)
    return InfoResponse(request: data)
}

Now if we build and run the app again and head back to POSTMAN
If we send the request again we will get a JSON response with the request returned

## Challenge: Your own Routes





























