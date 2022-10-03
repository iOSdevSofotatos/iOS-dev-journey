
![MVVM_design_pattern_for_swiftUI - Banner](https://user-images.githubusercontent.com/112722460/192390065-3ebd92fa-b63b-4bf7-a3c4-99011d68413d.png)

## What is meant by MVVM?

Model, View and ViewModel.
It is a design paradigm that makes it clear where all the dfferent components of your code go and how they interact with each other.

It also separates the user interface code, what we call the "View", from the backend or logic of our application, which we call the Model.

### Lets talk a bit more about the Model, the View and the mechanisms in MVVM that hook them up to each other.

### Model

- The Model is completely UI independent 
- The Model captures all the data and logic that describes "what" your application does
- The Model includes all the data and all the decision-making about what to do when a user interact with our app
- The Model is the single source of truth about the data that represents our app

### View

- The View is "how" your app is presented to your user
- The View is always going to be a reflection of the current state of the Model at all times
- The View is pretty much stateless, it doesn't need to store much in the way of state, because all the truth about the state of things is in the Model
- Any information we store in one of those @States will only have to do, with how the View is managing itself

### The mechanisms in MVVM that hook the Model and the View to each other

100% of what a View looks like is solely determined by what is in the implementation of its body var, nowhere else! and no when else either!, the only time your user interface is construced is when the system grabs the value of your body var

This is called coding declarative, because we are declaring what our View's UI looks like in its body var

Now the kind of coding we are probably used to is imperative coding, In an Imperative approach, we are calling functions one after another that are piecing the UI together over time, then again and again yet over time we are calling yet more functions to modify the UI as the user interacts with it. But each time we are hoping we don't make a mistake that puts the UI in some bad state or out of sync with the Model somehow. Now for us to ensure that this imperative code is gonna work properly, we pretty much need to anticipate every possible path through every function call that could affect our UI state, which is pretty much impossible! In a declarative approach we only have to look at one place to find the code that draws a View, its body var, and we only have to think of one path through the code accessing the body var. The system will never interact with our View in any other way than asking for the value of the body var and again it might do it at any time, but we don't care when we are asked either, because we are allways gonna be looking into the Model at its current state in the implementation of our body var. For our body var is kind of time insensitive, basically what you see in the body var is exactly what you get in the UI, and the only way it can change on screen is to redraw the whole thing

This locality of the code, (all the code lives entirely in this one place, a body var and the locality of time over which the UI is built, it gets built all at once not drop by drop every time, is why declarative coding is so well suited to user interface programming). As you can Imagine it results in code that is easier to write, eisier to read for sure, more reliable and more provable, (by provable you mean: can you prove that it does what you intend it to do?) For this to work though it does mean that every time something changes in the Model, you now have to ask all the Views that depend on that change for their body var. WHOA! For that to be practical, you have to have a system that is very efficient, it must only ask for the body vars of the Views that actually can change as a result of that latest change to the Model, otherwise you would be redrawing your UI constantly, your entire UI

This is what MVVM is all about! and this is why, in addition to be declarative, we also say that swift-UI is reactive. The View is always automatically reacting efficiently to changes in the Model.

At its core, MVVM's job really is just to hook up the Model to the View, so that any change in the Model will cause the affected parts, and only those affected parts of the View to be rebuilt, by accessing their body var to show that new state of the Model

## So how does this reactive stuff work in MVVM?

### ViewModel

Well it's all facilitated by this third element, The ViewModel. The ViewModel's job is to bind the View to the Model, so the changes in the Model cause the View to react and get rebuild.
Along the way, by the way, as it is doing that, it can also serve as a sort of interpreter (διερμηνέας) between the Model and the View. For example, our Model in this memory game we are writing it is really simple, it just a struct but you can imagine your model is an SQL database or maybe it is data coming over on the network, or making HTTP requests or something like that. It could get quite complicated over there in the Model. Now, we want the code in our View to be as simple as possible. We are writing it in this nice declarative way, so we don't want it to be gunked up with lots of data conversions and network calls and database access. So the ViewModel does all that!

It cleans up all that gunk for us if we are a View. For example, a ViewModel might represent the data from a SQL database Model to a View as an Array, or it might convert integers in some Model to floating point numbers or something, that the View would prefer. When we look at the functions and vars in the ViewModel that the View is allowed to call, they should be tuned to be "exactly what the View needs" to do its work. This is why we say, the ViewModel interprets (ερμηνεύει) the Model for the View.

The View Model also serves as a gatekeeper for the Model and ensures that access to the Model is like well-behaved, especially when it comes to changing the Model.

This interpreting and gatekeeping happens naturally, because of an important rule in MVVM. The View must always get data from the Model by asking for it from the ViewModel. Now the ViewModel never stores the data for the Model inside of itself. The Model is still the truth, it always is the truth. But the ViewModel is just an intermediary (μεσολαβητής) that is passing the data along from the Model to the View, perhaps cleaning it up or interpreting (ερμηνεύοντας) it as it flies by. 

But having this rule, that the View always has to go through the ViewModel, puts the ViewModel in a great spot to help with this reactive mechanism we talked about, that it so important to making all this work.

### Okay, so how does it do that?

First, a ViewModel is constantly noticing changes in the Model. Now it can do that in any way it wants. If your Model is a struct, like we are gonna have in our Memorize game. Then it is very easy. Swift has the ability to automatically track changes to a struct. If the Model where lets say something more complicated like a SQL database, it is quite easy to insert something into the database, which notifies you when there are changes. Or if the Model were something on the network, then obviously there are ways to get woken up when data appears from the network, and the ViewModel could do that as well.

But however it does it, it is obviously a must in MVVM that the ViewModel be able to track all changes in the Model. That is fundamental. Now when the ViewModel does notice a change, it immediately publishes "something changed" to the entire world, and anyone who is interested can listen for these pronouncements (ανακοινώσεις). 

### Well, why did it do it this way?

Why does it say "something changed" to the entire world? Well, for a very important reason, the ViewModel does not wanna have any connections to any of the Views that are using it to access the Model. This is a very important point. We never have a pointer or any other data structure in a ViewModel that knows anything about a View struct. It does have a general idea of how Views wanna look the Models data, of course, because it serves that interpreting function (ερμηνευτική λειτουργία) we talked about, but it has no connection to any specific View ever. And that is why it publishes to the whole world, "something changed"

It is up to the Views to sign themselves up to listen for these change announcements. The terminology we use here is that the Views are subscribing to what the ViewModel is publishing! And what is publishing is, something changed in the Model.

So when swiftUI sees that a publishing event has happened for a View that is subscribed to that ViewModel's announcement, it asks the View for his body var and redraws it. When the View is providing that body var, it is, of course, looking at the current state of the Model, and it is doing that through the ViewModel. So that way, the ViewModel can do its job of interpreting the Model data for the View. That's it! simple!

To make all this easy to set up, when a View sets itself up to access the Model through the ViewModel, it does so in a way that it simultaneously subscribes to the announcements that the ViewModel is making about changes in the Model. Therefore it can never be out of sync with the Model.

## something

We will go through all of these View setting itself up to access to the Model through the ViewModel mechanisms this week in demo

- ObservableObject (ViewModel)
- @Published (ViewModel)
- objectWillChange.send() (ViewModel)

- @ObservedObject (View)
- @Binding (View)
- .onReceive (View)
- @EnviromentObject (View)
- .enviromentObject() (View)

Above are some of the keywords involved about the MVVM mechanisms

### What about the other direction?

We talked about how the View is always finding out about changes and being rebuilt to show the latest state of the Model, but how does the Model get changed by touch events happening in the View?

Taps, swipes, navigating around in the UI. Presumably some of that is going to cause the Model to change!

We handle that by adding another respensibility to the ViewModel, processing user intent. We say it this way, because users are essentially expressing their intentions to change the Model through these touch events. And so we add functions to our ViewModel which allow the View to say, "oh the user just did something in my View, and by doing so, he intents the following conceptual thing to happen in the Model - whatever"

This thing that the user intents there is something a user understands. It is the user's intent after all! Then the ViewModel translates those intentions into specific modifications to the Model. 

For example, in an application where the Model is let's say a travel server with flight and hotel information, a typical user's intent might be something like "book this vacation". The user's intent is not something like contact travel server to confirm previous reservation with this ID and pass the data structure along to it, etc. Okay, the View just calls some simple function in the ViewModel, like "bookIt()" since that is the user's intent. The ViewModel does all that other stuff as part of its responsibility to facilitate communication between the View and the Model. Now it is not actually required to handle taps and swipes and stuff from the user in this way with these intentions. In other words there is nothing in swiftUI or in MVVM that enforces this, but it is a very kind of clean way to think about what is going on in your View and how it should be affecting your Model.

Now occasionally it is true that our Model might not really be anything more than just pure data store. In other words, there is very little in the way of user's intent other than just edit directly from fields in the UI. A ViewModel, in this case "brokers" gains access to the model mostly by just making the data in the Model more public than it might otherwise be. We ofthen call ViewModels that facilitate this sort of direct access to a Model a "Store". For kind of obvious reasons, right? We are just storing data in the Model nothing more. So sometimes, we will not have these explicit user intent functions in our Model, but if we do take this Store approach, we wanna be carefull that we don't end up starting to put code in our View, which is translating user intent into data store access. Okay, that sorta code wants to live in the ViewModel, not in a View. And if that started to happen we would want to that add user intent, as functions in our ViewModel, instead.

So the bottom line here is that simplicity and declarability are key to good View design and that good ViewModels support that, both with the way they present things via their interpreting function, but also by providing really sensible intents for the View to access.

So the View expresses the user's intent and the ViewModel changes the Model. Then what?

Well all the stuff we talked about earlier. The Model changes as a result of those intentions being expressed, The ViewModel notices the changes, it publishes the fact that things have changed to the world. The View has already subscribed to those changes when you had originally signed up to get that data, so it notices and the system redraws by getting the value of the View's body var again!

## This is the entire MVVM architecture!

![MVVM](https://user-images.githubusercontent.com/112722460/193465016-a784f13b-4bed-45f9-8003-10c4104913d5.jpg)

## How does the connection happen in MVVM between the Model, the View and the ViewModel

Well the job of the ViewModel is to be the intermediary between the Model and the View. So it needs to have a connection to the Model. And in fact, our ViewModel is going to create its own Model. That is not always the case for a ViewModel! Again sometimes the Model is a network database that already exists and it is going to connect to it. But, many times it creates the Model that it provides the window won, and it is the ViewModel's job to either make that thing persist on disk or on the network or something, or it is the kind of thing where the game just goes away when this ViewModel goes away, which is going to be true for our emojiMemoryGame.

While you are playing it is there, but if the ViewModel goes away, then the game is over it is gone. Because alot of ViewModels create their own Model, alot of times we will say that the "ViewModels" are the truth in our app. Now of course the model is the truth, But the ViewModel if it is creating its own Model, it is essentially the ViewModel itself is the truth. It stores that truth in the Model. But if you hear people say, "Oh and I'm creating this ViewModel, it is the source of truth for my UI", that is not an unreasonable way to look at it as well.

So our Model is just a struct so we are gonna make a var called Model that is gonna be of type MemoryGame

Lets talk about the ViewModel's role as gatekeeper to the Model. What do we mean by that gatekeeper?

It is the ViewModel's job really to protect the Model against ill-behaving Views or anyone else really who has access to the ViewModel. And one of the ways we do that is by making our Model private!

This keyword private, you are gonna see and be typing alot!

Private means that only the ViewModel's code itself can see the Model. That protects the Model against any Views reaching in and trying to change things. So we wanna make our vars and our funcs private, unless we are sure we wanna allow other structs and classes to access them.

Although sometimes fully private is a little too restricitve. For example, in our memory game, people really do need to see those cards, how else is the View is gonna draw the cards if it can't see them?

So there is another private called private(set), and private(set) tells Swift that other classes and structs can look at the "private(set) var model: MemoryGame", but they can't change it.
























## something else

The ViewModel has to modify the View somehow notify the View when the model changed and you can achieve that by making sure the ViewModel conforms to this: 
```swift
import swiftUI

class contentViewModel: ObservableObject {

}
```

Also we will instantiate the Model inside the ViewModel like this..

Model:
```swift
struct Person {
  var name: String
  var birthday: Date
}
```

ViewModel:

```swift
import swiftUI

class contentViewModel: ObservableObject {
  @Published private var alice = Person(name: "Alice", birthday: Date())
  
  var name: String {
    alice.name
  }
  
  var age: String {
    // date magic -> age String
    return "32"
  }
}
```

We also need to have our ViewModel inside of our View and observe the changes, there are several ways to achieve that

View:

```swift
import swiftUI

struct ContentView: View {
  @StateObject var viewModel = ContentViewModel()
  var body: some view {
    Text(viewModel.name)
  }
}
```

Lastly lets make a button to change the name when pressed and we will tell the ViewModel to do that, instead of directly accessing the Model

To do that the ViewModel needs to have an intent

ViewModel:

```swift
import swiftUI

class contentViewModel: ObservableObject {
  @Published private var alice = Person(name: "Alice", birthday: Date())
  
  var name: String {
    alice.name
  }
  
  var age: String {
    // date magic -> age String
    return "32"
  }
  
  // intent
  func changeName(_ name: String) {
    alice.name = name
  }
}
```









A large Application can have many different Models and of course dozens or even hundreds of Views

That body var must always return something based on the current state of the Model, that is what it does, that is almost the definition of a body var
Return a UI that reflects the state of the Model

```swift
import swiftUI

struct Memorize: View {
  var body: some View {
    Text("hello world")
  }
}
```
