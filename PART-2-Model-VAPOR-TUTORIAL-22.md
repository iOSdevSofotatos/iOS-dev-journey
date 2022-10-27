In this section, we will learn how to leverage fluent to create a Model and how to use controllers

We deleted all 3 files from "Controllers", "Migrations" and "Models" folders

Then we removed the line referencing in "configured.swift" (app.migrations.add(CreateTodo()) removed)

And we also removed the to-do controller registration in "routes.swift" (try app.register(collection: TodoController() removed)

Now in Sources/App/Models we create a new file called acronym.swift
Inside that file we import fluent and vapor and then create a final class acronym and make it conform to model
The first thing that we need in the model is an ID and this will be a UUID and it also has to be optional (var id: UUID?), because when we first create it, it won't have been saved in the database and won't have been given an ID. 
We also need to annotate (σχολιάζω) the ID with the ID property wrapper so that fluent knows it's the ID for the Mode:
@ID
var id: UUID?

The algorithm will also have 2 properties on it, a short version and a long version both of which will be strings so let's go ahead and cr eate them, They will also both be annotated with a field property record with a key:

@Field(key: "short")
var short: String

@Field(key: "long")
var long: String

Next we need to create the initializers for the class, so that we can create the models, we will need an empty initializer as well as fluent uses this to construct the models when reading them from the database:

init() {}

init(id: UUID?, short: String, long: String) {
    self.id
    self.short
    self.long
}

Finally, we must define the schema which is the table name:

static let schema = "acronyms"

Let's add an empty protocol conformance extension at the bottom of the file to make Acronym conform to content, so that we can easily send and receive acronyms with vapor:

extension Acronym: Content {}

To use the model with fluent, we need to set up a migration. A migration tells fluent how to prepare the database, such as adding the right table or seeding data (Data seeding is the process of populating a database with an initial set of data). This will be run, when your application launches

This will be run when our application launches, only if it has never been run by fluent before. This is important to note, if we are changing our models.

### Migrations folder
Now lets go ahead and create a new file in Sources/App/Migrations called CreateAcronym.swift

Lets first import fluent and create a new type called create acronym that conforms to migration

Now we implement the prepare and revert functions: 

struct CreateAcronym: Migration {
    func prepare(on database: FluentKit.Database) -> EventLoopFuture<Void> {
        <#code#>
    }
    
    func revert(on database: FluentKit.Database) -> EventLoopFuture<Void> {
        <#code#>
    }
}


In prepare we want to define the schema that we want to use:
func prepare(on database: FluentKit.Database) -> EventLoopFuture<Void> {
    // define the schema
    database.schema("acronyms")
}
    
Also the schema matches the schema name in the model
 
Then lets define the ID column and the two fields of the two properties:
            
func prepare(on database: FluentKit.Database) -> EventLoopFuture<Void> {
    // define the schema
    database.schema("acronyms")
        .id()
        .field("short", .string, .required)
        .field("long", .string, .required)
}
                
Finally lets call 'create' to tell fluent to create the table:

func prepare(on database: FluentKit.Database) -> EventLoopFuture<Void> {
    // define the schema
    database.schema("acronyms")
        .id()
        .field("short", .string, .required)
        .field("long", .string, .required)
        // create the table
        .create()
}                
                
Next, we have to implement the revert function, again we will tell fluent to use the acronym schema, but this time we will just call delete:                
                
func revert(on database: FluentKit.Database) -> EventLoopFuture<Void> {
    database.schema("acronyms").delete()
}               
                
Now we need to tell fluent about the migration, so that it will execute it. Let's head back to "configure.swift" and add the following after the setup for the database:

app.migrations.add(CreateAcronym())                
 
This will ensure the migration is run next time we run our app
           
Finally we actually need to set up the database, to get started we need to have Docker installed in our Mac. Docker is what let us run containers on our Mac
            
Now if we go to the terminal, we should be able to run the Postgres container
To achieve the above, run the following command in the terminal:
            
docker run --name postgres -e POSTGRES_DB=vapor_database -e POSTGRES_USER=vapor_username -e POSTGRES_PASSWORD=vapor_password -p 5432:5432 -d postgres

This may take some time to run for the first time, what this command does is start a container callled Postgres with these enviroment variables which allows us to set up the database, username and password. Expose this port from the container, run it in the background and use the Postgres default image. Once that has finished, Postgres should be running. We can checklist with the "docker ps" command, we can see it running       
                
Finally we need to tell Vapor to run the migration, when it launches. We can either run them on every app launch or for a production app with lots of instances. Choose when to run them, at the bottom of configure.swift add the following:
            
try app.autoMigrate().wait()
            
The above command will run the migrations on every app launch
            
Finally set the log level to debug, so that we can see what is going on easily. At the top of configure, add the following:
            
app.logger.logLevel = .debug
            
Now if we build and run the app, it should launch without any issues and we should see the migrations being succesful in the console
                
                
Next chapter - Controllers & CRUD             
          
          
          
          
          
        
          








































































































































