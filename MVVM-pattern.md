
![MVVM_design_pattern_for_swiftUI - Banner](https://user-images.githubusercontent.com/112722460/192390065-3ebd92fa-b63b-4bf7-a3c4-99011d68413d.png)

## What is meant by MVVM?

Model, View and ViewModel.
It is a design paradigm that makes it clear where all the dfferent components of your code go and how they interact with each other.

It also separates the user interface code, what we call the "View", from the backend or logic of our application, which we call the Model.

### Lets talk a bit more about the Model, the View and the mechanisms in MVVM that hook them up to each other.

- The Model is completely UI independent 
- The Model captures all the data and logic that describes "what" your application does
- The Model what your application actually is and does
- The Model includes all the data and all the decision-making about what to do when a user interact with our app
- The Model is the single source of truth about the data that represents our app


### View

- The View is "how" your app is presented to your user
- The View is always going to be a reflection of the current state of the Model at all times
- The View is pretty much stateless, it doesn't need to store much in the way of state, because all the truth about the state of things is in the Model
- Any information we store in one of those @States will only have to do, with how the View is managing itself
- 100% of what a View looks like is solely determined by what is in the implementation of its body var, nowhere else! and no when else either!, the only time your user interface is construced is when the system grabs the value of your body var

This is called coding declarative, because we are declaring what our View's UI looks like in its body var

Now the kind of coding we are propably used to is imperative coding, In an Imperative approach, we are calling functions one after another that are piecing the UI together over time, then again again yet over time we are calling yet more functions to modify the UI as the user interacts with it. But each time we are hoping we don't make a mistake that puts the UI in some bad state or out of sync with the Model somehow. Now for us to ensure that this imperative code is gonna work properly, we pretty much need to anticipate every possible path through every function call that could affect our UI state, which is pretty much impossible! In a declarative approach we only have to look at one place to find the code that draws a View, its body var, and we only have to think of one path through the code accessing the body var. The system will never interact with our View in any other way than asking for the value of the body var and again it might do it at any time, but we don't care when we are asked either, because we are allways gonna be looking into the Model at its current state in the implementation of our body var. For our body var is kind of time insensitive, basically what you see in the body var is exactly what you get in the UI, and the only way it can change on screen is to redraw the whole thing 

This locality of the code, (all the code lives entirely in this one place, a body var and the locality of time over which the UI is built, it gets built all at once not drop by drop every time, is why declarative coding is so well suited to user interface programming. As you can Imagine it results in code that is easier to write, eisier to read for sure, more reliable and more provable, ( by provable you mean: can you prove that it does what you intend it to do?) For this to work though it does mean that every time something changes in the Model, you now have to ask all the Views that depend on that change for their body var. WHOA! For that to be practical, you have to have a system that is very efficient, it must only ask for the body vars of the Views that actually can change as a result of that latest change to the Model, otherwise you would be redrawing your UI constantly, your entire UI

This is what MVVM is all about! and this is why, in addition to be declarative, we also say that swift-UI is reactive. The View is always automatically reacting efficiently to changes in the Model.

At its core, MVVM's job really is just to hook up the Model to the View, so that any change in the Model will cause the affected parts, and only those affected parts of the View to be rebuilt, by accessing their body var to show that new state of the Model

### So how does this reactive stuff work in MVVM?

Well it's all facilitated by this third element, The ViewModel. The ViewModel's job is to bind the View to the Model, so the changes in the Model cause the View to react and get rebuild.
Along the way, by the way, as it is doing that, it can also serve as a sort of interpreter between the Model abd the View. For ex, our Model in this memory game we are writing it is really simple, it just a struct but you can imagine your model is an SQL database or maybe it is data coming over on the network, or making HTTP requests or something like that. It could get quite complicated over there in the Model. Now, we want the code in our View to be as simple as possible. We are writing it im this nice declarative way, so we don't want it to be gunked up with lots of data conversions and network calls and database access. So the ViewModel does all that!

It cleans up all tha gunk for us if we are a View. For ex, a ViewModel might represent the data from a SQL database Model to a View as an Array, or it might convert integers in some Model to floating point numbers or something, that the View would prefer. When we look at the functions and vars in the ViewModel that the View is allowed to call, they should be tuned to be "exactly what the View needs" to do its work. This is why we say, the ViewModel interprets the Model for the View.

The View Model also serves as a





A large Application can have many different Models and of course dozens or even hundreds of Views

That body var must always return something based on the current state of the Model, that is what it does, that is almost the definition of a body var
Return a UI that reflects the state of the Model
