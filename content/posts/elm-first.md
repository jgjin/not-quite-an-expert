---
title: "Elm, or my first time with functional"
date: 2019-12-27
draft: false
tags: ["programming", "technology"]
---
# Introduction
[The Pomodoro Technique](https://en.wikipedia.org/wiki/Pomodoro_Technique/) employs roughly 30-minute cycles, 25 minutes working on a task then 5 minutes taking a break, to get work done. Unfortunately, some tasks, particularly programming, involve [a state of flow](https://en.wikipedia.org/wiki/Flow_(psychology/)) which would get interrupted by 30-minute cycles. The simpler Flow Time Technique allows for flow by relaxing the 25-/5-minute constraint in favor of deciding on your own when to take breaks.

I wanted to create a tool to help me practice the Flow Time Technique. I also wanted to learn a new programming language. So I wrote an Elm application to help me practice the Flow Time Technique. In case you're interested, the Flow Time Technique tool I wrote is available at [flow-time.xyz](https://flow-time.xyz/), with code on [Github](https://github.com/jgjin/flow-time-elm/).
# What is Elm?
Elm is a relatively new programming language which:
1. Transpiles to JavaScript for web applications
2. Is pure functional (more on that later)
3. Has a beginner-friendly compiler (more on that later)
# Elm web application structure
> Note: for a more detailed tutorial, see [Beginning Elm: A gentle introduction to the Elm programming language](https://elmprogramming.com/). This summary assumes basic knowledge of web applications, such as previous experience with JavaScript.

For a basic Elm web application, you should use [`Browser.element`](https://package.elm-lang.org/packages/elm/browser/latest/Browser#element), which has the following type:
```Elm
element :
    { init : flags -> ( model, Cmd msg )
    , view : model -> Html msg
    , update : msg -> model -> ( model, Cmd msg )
    , subscriptions : model -> Sub msg
    }
    -> Program flags model msg
```
Woah. Since I'm not quite an expert, let's break this down into understandable chunks.

Basically, we want 4 functions:
1. `init`: initializing our web application's model, or application state
2. `view`: turning our model, or application state, into viewable HTML
3. `update`: updating our model, or application state, based on new information
4. `subscriptions`: subscribing to events (e.g. clock ticks every `m` milliseconds) to update our model, or application state
## Model
All 4 of these functions relate to "model, or application state." The term "model" is more common in discussion about Elm. However, those coming from other languages, especially React, will be more familiar with the term "state." Conceptually, the model represents the information your application holds at some point in time.

Models in Elm are very similar in structure to structs or objects in other programming languages. In the Flow Time Technique tool, our model looks like:
```Elm
type alias Model =
    { status : Status
    , accumDuration : Duration.Duration
    , tasks : Array.Array Task
    , nextTaskId : Int
    , nextTaskName : String
    }
```
In Elm, a collection of typed fields is known as a record. We see that our model is a record with 5 fields: `status`, `accumDuration`, `tasks`, `nextTaskId`, and `nextTaskName`.
## `init`
`init` isn't terribly interesting, at least not in this case. We just initialize the fields specified in the model:
```Elm
init : () -> ( Model, Cmd Msg )
init _ =
    ( { status = Idle
      , accumDuration = Duration.seconds 0
      , tasks =
            Array.fromList
                [ { id = -2
                  , name = "Shuck corn"
                  , accumDuration = Duration.seconds 0
                  , hover = None
                  }
                , { id = -1
                  , name = "Peel bananas"
                  , accumDuration = Duration.seconds 0
                  , hover = None
                  }
                ]
      , nextTaskId = 0
      , nextTaskName = ""
      }
    , Cmd.none
    )
```
In case you're curious, the first line is a function declaration. The function declaration basically declares what I said before:
```Elm
init : () -> ( Model, Cmd Msg )
```
1. `()`: Take in nothing[^1]
2. `->`: and return
3. `( Model, Cmd Msg )`: a tuple of a model record and a command.

[^1]: A more complicated `init` would take flags into account; in our relatively simple case we don't need to handle flags.

Don't worry about `Cmd`. Commands are used to execute operations with [side effects](https://en.wikipedia.org/wiki/Side_effect_(computer_science)/), which are rare in Elm because Elm aims to be pure functional. We don't use the `Cmd`, so we put `Cmd.none` as a placeholder.

We don't technically need a function declaration because the Elm compiler tries its best to infer types. The function declaration helps keep our code a bit more readable and helps the Elm compiler catch typing mistakes in our code.
## `view`
`view` also isn't that interesting. We render HTML based on our model:
```Elm
view : Model -> Html Msg
view model =
    div [ simpleClassList [ "row", "min-vh-100", "flex-column", "flex-md-row" ] ]
        [ <inner HTML content of div omitted for brevity> ]
```
I've omitted most of the body of the function because it's just more HTML. Notice how Elm structures HTML: the type of tag, followed by a list of attributes, followed by a list of inner HTML content of the tag. 
## `update`
To understand `update`, we need to look at `Msg`. Unlike `Model`, `Msg` isn't a record, it's a custom type:
```Elm
type Msg
    = Adding
    | Deleting Int
    | Toggling Int
    | UpdatingName String
    | UpdatingDuration Time.Posix
    | Hovering Int Hover
```
Custom types in Elm are a lot like enums from other languages. Our type `Msg` is one of `Adding` or `Deleting` (wrapping an integer) or `Toggling` (wrapping an integer), etc. We can handle the custom type cleanly with a case block:
```Elm
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        Adding -> <handling Adding omitted for brevity>

        Deleting id -> <handling Deleting omitted for brevity>

        Toggling id -> <handling Toggling omitted for brevity>

        UpdatingName newName -> <handling UpdatingName omitted for brevity>

        UpdatingDuration _ -> <handling UpdatingDuration omitted for brevity>

        Hovering hoverId hover -> <handling Hovering omitted for brevity>
```
Notice we can give the wrapped data a name. e.g. `UpdatingName` wraps a `String`, which we call `newName`.
## `subscriptions`
`subscriptions` is a bit less intuitive than the rest. It helps to have an example. In our case, because we are keeping track of time, we want to the update model on (i.e. subscribe to) regular time intervals. To do so, we can use the `Time.every` function, which takes `m` milliseconds and a message to emit every `m` milliseconds as arguments[^2]:
[^2]: By nature of being pure functional I guess, Elm is particular in defining "argument" and "parameter" separately. From [Beginning Elm](https://elmprogramming.com/function.html#function-application): "The terms parameter and argument are often used interchangeably although they are not the same. [A]n argument is used to supply a value when applying the function (e.g. `escapeEarth 11`) whereas a parameter is used to hold onto that value in function definition (e.g. `escapeEarth myVelocity`)."
```Elm
subscriptions : Model -> Sub Msg
subscriptions _ =
    Time.every 1000 UpdatingDuration
```
## Running the application
During development, we can use [Elm reactor](https://elmprogramming.com/elm-reactor.html) to test our code. In production, we transpile Elm into JavaScript (with the `elm make` command) and attach it to an HTML document:

```Html
<body>
    <div class="container-fluid">
        <div id="elm-app"></div>
    </div>
    <script src="js/src_flowtime.js"></script>
    <script>
     var app = Elm.FlowTime.init({
         node: document.getElementById("elm-app")
     });
     <rest of script omitted for clarity>
    </script>
</body>
```
# Elm reflections
## Pros
### 1. Less buggy
I wrote the Flow Time Technique tool in React before. It was full of bugs, such as not keeping track of time correctly when changing the list of tasks. Yes, I certainly could've bugfixed or re-wrote the React version to address the issues. However, Elm forced me to be more deliberate with application state/behavior, resulting in only two minor bugs which I fixed immediately. 

Being less buggy is a common selling/talking point of functional programming languages. In dicussions/aggressive forums, functional is framed in contrast to imperative programming. [Functional advocates](https://elmprogramming.com/pure-functions.html#benefits-of-pure-functions) argue that by encouraging immutable data and discouraging [side effects](https://en.wikipedia.org/wiki/Side_effect_(computer_science)/), functional programming results in functions that are less bug-prone and easier to understand, debug, test, and scale.

In this case, I at least saw the less bug-prone component.
### 2. Beginner-friendly compiler
The Elm compiler is well-known for being friendlier to beginners than other compilers (looking at you, g++). For example, when I run `elm init` to initialize an Elm project, I get the following message:
```
Hello! Elm projects always start with an elm.json file. I can create them!

Now you may be wondering, what will be in this file? How do I add Elm files to
my project? How do I see it in the browser? How will my code grow? Do I need
more directories? What about tests? Etc.

Check out <https://elm-lang.org/0.19.1/init> for all the answers!

Knowing all that, would you like me to create an elm.json file now? [Y/n]: n
Okay, I did not make any changes!
```
As a whole, I support the concept of programming tooling making programming more understandable to beginners. I think more people should write code, or at least know the basics. It's good to see tools that strive to promote understanding in newbies, rather than employ the "git gud" attitude common in computer science.
### 3. More performant
[Apparently, Elm is faster than React and Angular](https://elm-lang.org/news/blazing-fast-html-round-two/), so there's that. Elm claims to accomplish this via its virtual DOM and preference for more optimizable data structures and operations.
## Cons
### 1. Less intuitive
More people start programming through imperative programming. I have [fairly good data](https://insights.stackoverflow.com/survey/2019#technology) to back up this notion. 

I had a discussion with a friend about why this is the case. I think some part is that imperative is more intuitive to most people. I talk about programming to non-programming friends in imperative language and I usually talk about non-programming in imperative language as well:
> Programming to non-programming: "for each file, read the contents if it exists" rather than "filter the files on existence, then map reading the contents"

> Non-programming: "keep a running counter on the number of defective units" rather than "sum over the defective units"

My friend disagrees; they thinks imperative is only the norm because of precedence. Either way, we both agree that empirically, imperative tends to be people's first experience with programming. That makes pure functional languages like Elm harder to pick up.[^3]

[^3]: Luckily, I had been using Rust for a while before starting this Elm project, which I think demonstrates the power of languages that combine imperative and functional concepts.
### 2. Less readable
I mean just look at this!
```Elm
if id == activeId then
    ( { model
        | status = Resting
        , tasks = tasks
      }
    , Cmd.none
    )
```
Or this:
```Elm
Array.fromList
    [ { id = -2
      , name = "Shuck corn"
      , accumDuration = Duration.seconds 0
      , hover = None
      }
    , { id = -1
      , name = "Peel bananas"
      , accumDuration = Duration.seconds 0
      , hover = None
      }
    ]
```
Or this:
```Elm
[ li [ simpleClassList [ "nav-item", "m-1" ] ]
    [ div [ class "card", style "min-width" "10rem" ]
        [ div [ class "card-header" ]
            [ form [ class "w-100", style "min-height" "2.5rem", onSubmit Adding ]
                [ input [ class "w-100", type_ "text", value nextTaskName, onInput UpdatingName ] []
                ]
            ]
        , div [ class "card-body" ]
            [ p [ simpleClassList [ "text-center", "m-0" ] ]
                [ text "00:00:00" ]
            ]
        ]
    ]
]
```
These aren't pathological examples, mind you. The `elm-format` tool, which follows the Elm community's standard, formats the code like this. Don't believe me that it's the standard Elm format? Look at the [Beginning Elm book](https://elmprogramming.com/creating-post-module.html).

I'm sure I _could_ get used to this. _Why though?_ It makes me feel dirty. Regardless of imperative or functional, I vastly prefer the Rust syntax for function declarations:
```Rust
pub fn update(
    message: &Msg,
    model: &Model,
) -> (Model, Cmd<Msg>) {
    // body goes here
}
```
or the Python syntax for lists:
```Python
self.tasks = [
    {
        "id": -2,
        "name": "Shuck corn",
        "accumDuration": timedelta(seconds=0),
        "hover": None,
    },
    {
        "id": -1,
        "name": "Peel bananas",
        "accumDuration": timedelta(seconds=0),
        "hover": None,
    },
]
```
### 3. Less mature
Elm is on version 0.19 right now. That means even core features of the language aren't technically stable. There's a noticeable lack of packages available for Elm, with even fewer supporting the newest version of 0.19. I actually ran into a package I wanted to use that didn't support Elm 0.19, and no other package had the same functionality.

The Elm community is very friendly, or so I've heard. You might need to implement your own functionality that you would expect in packages of other languages, though the Elm community would probably be eager to help if you ask.
# Conclusion
At this point, I'm not sure I would use Elm for a while. I would wait until the language and its packages are more mature. In the meantime, I've started using [flow-time.xyz](https://flow-time.xyz/).
