
Uses Fluent to interact and store that information in a PostgreSQL database
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

# Creating Your Own Routes






































Heroku when it is time, until then keep it local




























