
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

Now the kind of coding we are propably used to is imperative coding, In an Imperative approach, we are calling functions one after another that are piecing the UI together over time, then again again yet over time we are calling yet more functions to modify the UI as the user interacts with it. But each time we are hoping we don't make a mistake that puts the UI in some bad state or out of sync with the Model somehow. Now for us to ensure that this imperative code is gonna work properly, we pretty much need to anticipate every possible path through every function call that could affect our UI state, which is pretty much impossible! In a declarative approach we only have to look at one place to find the code that draws a View, its body var, and we only have to think of one path through the code accessing the body var. The system will never interact with our View in any other way than asking for the value of the body var

A large Application can have many different Models and of course dozens or even hundreds of Views

That body var must always return something based on the current state of the Model, that is what it does, that is almost the definition of a body var
Return a UI that reflects the state of the Model
