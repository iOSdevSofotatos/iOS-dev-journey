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
    func prepare(on database: FluentKit.Database) -> NIOCore.EventLoopFuture<Void> {
        <#code#>
    }
    
    func revert(on database: FluentKit.Database) -> NIOCore.EventLoopFuture<Void> {
        <#code#>
    }
}


In prepare we want to define the schema that we want to use: (database.schema("acronyms")
                
                
                
                
                
                
                
                
                
                
                
                
                
           
 
          
                
                
                
                
                
          
          
          
          
          
        
          








































































































































