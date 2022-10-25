# The fundamentals of how the web and HTTP operate

This chapter explains what you need to know about HTTP,its methods,
and its most common response codes

HyperText Transfer Protocol, or HTTP, is the foundation of the web. 
Each time you visit a website, your browser sends HTTP requests to and receives responses from the server.
Many dedicated apps — ordering coffee from your smartphone, streaming video to your TV,
or playing an online game — use HTTP behind the scenes

At its core, HTTP is simple. There’s a client — an iOS application, a web browser or even a simple cURL session — and
a server. The client sends an HTTP request to the server which returns an HTTP response

![original](https://user-images.githubusercontent.com/112722460/197791064-8116bb72-6426-42b1-b62e-aece9f562a34.jpg)

## HTTP requests
An HTTP request consists of several parts:

- The request line: This specifies the HTTP method to use, the resource requested and the HTTP version.
GET /about.html HTTP/1.1 is one example
- The host: The name of server to handle the request. This is needed when multiple servers are hosted at the same address
- Other request headers such as Authorization, Accept, Cache-Control, Content-Length, Content-Type etc
- Optional request data, if required by the HTTP method

The HTTP method specifies the type of operation requested by the client
The HTTP specifications define the following methods:

- GET
- HEAD
- POST
- PUT
- DELETE
- CONNECT
- OPTIONS
- TRACE
= PATCH




