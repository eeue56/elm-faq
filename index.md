---
title: Elm FAQ
layout: page
---

**This page is mostly for folks *learning* Elm.** It aggregates questions that are commonly
asked on [the Slack channel][slack], [the IRC channel][irc], or [the mailing list][list].
Those are all friendly and helpful places to go if you do not find the answer to your
question here!

[slack]: http://elmlang.herokuapp.com/
[irc]: http://webchat.freenode.net/?channels=elm
[list]: https://groups.google.com/forum/#!forum/elm-discuss

* Contributions to [this document](https://github.com/elm-community/elm-faq)
  are welcome!

* This document is about the current version of Elm (0.17). See also the
  [Elm 0.17 FAQ](17.html) about upgrading to Elm 0.17. See the [Elm 0.16 FAQ](16.html)
  about that older version of Elm.

#### Contents

<div id="elm-content"></div>


### What is the difference between `type` and `type alias`?

`type` defines and names a new type (which behaves like an enum with data
attached), and `type alias` gives a name to an existing type.

`type alias` isn't creating a distinct type, it is literally just giving a name to an existing type. A type alias will save you keystrokes, but do nothing more. [ref](https://groups.google.com/forum/#!topic/elm-discuss/YgRqI5s2S7Y)




### Why does elm-repl (or elm-make) report "cannot find module 'Html'"?
You need to install the Html module:

    elm package install elm-lang/html

Several modules are [available by default](http://package.elm-lang.org/packages/elm-lang/core/latest) in the base Elm tools but other common modules like Html have to be installed in the working directory before they can be used in elm-make, elm-repl, and elm-reactor.



### Why does elm-repl report a syntax problem for any type annotation, such as for `fib : Int -> Int`?

elm-repl does not support type annotations at all. If you try you'll see a "syntax problem" message saying something like "I ran into something unexpected when parsing your code!" and pointing to the colon.

To use type annotations you will have to use elm-reactor or [elm-lang.org/try](http://elm-lang.org/try), or build and run a complete app.



### How do I know what package name to use for `elm package install`?
Search on [package.elm-lang.org](http://package.elm-lang.org/) for the module name and use the package name that you find there.



### How can I write debugging information to the console?
Wrap any value with `Debug.log “some message”` and that message and the value will be written to the javascript console every time the value is evaluated. For example:

```haskell
case Debug.log "action" action of
```

If you want to just log a message and value without using that value, try this in a `let` clause:

```haskell
_ = Debug.log "my message" someValue
```

Also see [`Debug.crash`](http://package.elm-lang.org/packages/elm-lang/core/latest/Debug#crash)
which gets special treatment from the compiler to provide additional information in the output.



### How do I install an older version of Elm, 0.16 for example?

    npm install -g elm@0.16

If you need to switch between multiple versions of elm, consider [elmenv](https://github.com/sonnym/elmenv).



### What does `=>` mean?
A common idiom is to define the `(=>)` operator as a synonym for the `(,)` operator that constructs tuples. This makes a shorthand for long lists of tuple pairs, often used with the Html.style property.  So `["color" => "red", "padding" => "2px"]` means `[("color", "red"), ("padding", "2px")]`.

Note: Operators cannot be defined in elm-repl.



### What's the difference between `Html Msg` and `Html msg`?
`msg` is a placeholder used when the HTML doesn't send any messages of type `Msg` (as in `type Msg = ...`). This is just as `a` is used as a placeholder in `List a` when the list is of any type, rather than of strings (`List String`) or integers (`List Int`) etc.. `msg` means "message of any type".



### How can I output literal HTML and avoid escaping of entities?
Use the `innerHTML` property. For example:

```haskell
span [ property "innerHTML" (Json.Encode.string "&copy;") ] []
```



### What does `()` mean?

It is the empty tuple or [unit type](https://en.wikipedia.org/wiki/Unit_type). It serves much like "void", "null", or "None" in other languages.



### What good is the `<|` operator if it is just function application?

It has lower precedence than function application expressed by adjacency (e.g. `sqrt x`) and is right-associative, and so it can be used instead of parentheses to group function arguments. For example, `a b ( c d )` is the same as `a b <| c d`, and `f ( g ( h x ) ) )` can be written as `f <| g <| h x`.  More concretely, `max 3 (sqrt x)` can be written as `max 3 <| sqrt x`. [^application]

[^application]: Function application and the `<|` operator are discussed at some length [here](https://groups.google.com/d/msg/elm-discuss/-PLj_eamKVQ/Zzo7iNx2FgAJ).

Note: The `<|` operator is essentially the same as `$` in Haskell.



### What are the Elm operator precedences and associativities?

See an [Elm operator precedence table](operators.html).
See also [Basics.elm](https://github.com/elm-lang/core/blob/master/src/Basics.elm).



### How can I use multiple Elm programs on the same page?

You can compile multiple modules into a single elm.js and then instantiate whatever module you need on the appropriate div. [^multipleModules]

    elm-make A.elm B.elm --output elm.js

[^multipleModules]: Use of multiple main modules in one application is discussed [here](https://groups.google.com/d/msg/elm-discuss/eEJgNnl99ps/keWXnn1KCwAJ).


### Does the main module file have to be named "Main.elm"?

No, that is just a convention. Any module containing a `main` function of type `Program Never` can be an entry point to an Elm program.

For example, if both Foo.elm and Bar.elm contain an appropriate `main` function,
compiling via "elm-make Foo.elm Bar.elm \-\-output elm.js" creates an elm.js file
such that both `Elm.Foo.embed(someElement)` and
`Elm.Bar.embed(someOtherElement)` can be used from the same HTML file.


### Why doesn't the Elm compiler find the Native code in a module that I cloned from github?

To use native code not installed by `elm-package` you need to add this to your elm-package.json file:

    "native-modules": true,



### Why, when I import a module that defines a type, does the compiler know about the type name but not its constructors?

You need to import the module in one of the following ways:

    import MyModule exposing (MyType(..)) -- exposes all constructors
    import MyModule exposing (MyType(MyConstructor)) -- exposes only MyConstructor

Just exposing `MyType` without the `(..)` will leave the constructors undefined, while exposing `(MyConstructor)` will expose `MyConstructor` but hide `AnotherConstructor`.

Similarly, the module itself must export the constructors.

    module MyModule exposing (MyType(..)) -- exposes all constructors
    module MyModule exposing (MyType(MyConstructor)) -- exposes only MyConstructor

However, there are reasons for [keeping tags and record constructors secret](http://package.elm-lang.org/help/design-guidelines#keep-tags-and-record-constructors-secret).

You can also choose to export a type without exporting any constructors like this (just make sure you expose some other way of creating values of your type):

    module MyModule exposing (MyType, myOtherFunctions)



### Where can I use type annotations?

In addition to the top-level, type annotations can also be applied to `let` expressions.

```haskell
let
  hypotenuse : Float -> Float -> Float
  hypotenuse a b =
    sqrt (a^2 + b^2)
in
  hypotenuse 3 4
```



### How can I join the elmlang.slack.com community?
Sign up at [elmlang.herokuapp.com](http://elmlang.herokuapp.com/).



### How can I recover when elm-make fails with errors like "... openFile: does not exist"?

That can happen when switching between elm versions. Try removing all of elm-stuff or just the build-artifacts:

    rm -r elm-stuff/build-artifacts



### How do I install an Elm package that has not been published to packages.elm-lang.org for use in my project?

Clone the package into a separate directory and add its directory path to the `source-directories` section of the elm-package.json file for your project. As usual, you will also have to install any dependencies of the package. If the package includes any native javascript code you will have to also add `native-modules: true` to elm-package.json.



### How can I parse Json into Elm data?

Currently you have to write the [parsing
code](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode).
Other than for [data passed over ports](http://elm-lang.org/guide/interop#customs-and-border-protection) there is no automatic conversion (and even
there, experts recommend writing the parser manually to be able to handle error
cases).

The [json-to-elm](https://github.com/eeue56/json-to-elm) tool is a Python script that generates Elm code to parse a given example of Json. That tool is available online at [json2elm.com](http://json2elm.com).

There are additional Json parsing tools in the [elm-json-extra](http://package.elm-lang.org/packages/circuithub/elm-json-extra/latest/Json-Decode-Extra) package.

The [Elm Json Decode interpreter](http://simonh1000.github.io/decoder/) is an online tool allowing you to experiment with decoders and test them in a simple context.

### How can I pass an object over ports?

If you have a JS object, you have to tell Elm what types its values have. You can use a type alias to do this. So, for a [BigRational](https://github.com/peterolson/BigRational.js):

```elm
type alias BigRational = 
    { num : { value : Int, sign : Bool, isSmall : Bool }
    , numerator : { value : Int, sign : Bool, isSmall : Bool }
    , denom : { value : Int, sign : Bool, isSmall : Bool }
    , denominator : { value : Int, sign : Bool, isSmall : Bool }
    }
```

### How can I report a compiler error message that could be better?

Report the problem at the [error-message-catalog issue tracker](https://github.com/elm-lang/error-message-catalog/issues)
with an [short, self-contained, correct, example](http://sscce.org/) showing both the program and the problematic error messages.



### Does Elm have HashMaps?

The core [Dict](http://package.elm-lang.org/packages/elm-lang/core/latest/Dict) package provides a dictionary mapping unique keys to values. There are some restrictions on key value types; in particular, records cannot be keys.


### Why does my app fail immediately with a console error of "Uncaught TypeError: Cannot read property 'appendChild' of null"?

Make sure that you are calling Elm's javascript `embed` function *after* the
referenced container has been defined in the HTML file.

Similarly, Elm's `fullscreen` function should be called only after the page body has started.

Good practice is to call `embed` or `fullscreen` at the end of the document body.


### How can I load CSS (or other resources) in elm-reactor?

It's not easy. Elm-reactor can serve CSS files and so you can write a custom
HTML file that links in your CSS and the elm.js generated by Elm and then load
that HTML file in elm-reactor. But you have to generate the elm.js outside of
elm-reactor; it is not automatically built or rebuilt on changes to the Elm source.

When you click an Elm source file in elm-reactor it compiles just that file (and
any dependencies) and sends back the generated javascript which then runs and
displays the full page view of the program. So to add any CSS you have to do it
in the Elm program itself. Here is an example (thanks to @pdamoc):

```elm
import Html exposing (..)
import Html.Attributes exposing (..) 

withStyle html = 
  div []
  [ node "style" [type' "text/css"] 
    [text "@import url(https://cdnjs.cloudflare.com/ajax/libs/bulma/0.1.2/css/bulma.min.css)"]
  , html
  ]

main =
  div [] 
    [ a [class "button is-primary"] [text "Primary"]
    , a [class "button is-info"] [text "Info"]
    ]
  |> withStyle

```

For a more comprehensive solution, see the next question.


### How can I integrate Elm development into a larger app with CSS files and other resources?

Elm-reactor is *not* appropriate for that; it's geared toward simple pure-Elm applications.

Consider using other hot-reload tools:
[elm-live](https://github.com/tomekwi/elm-live),
[elm-hot-loader](https://github.com/fluxxu/elm-hot-loader),
[elm-webpack-starter](https://github.com/moarwick/elm-webpack-starter),
[gulp-elm](https://github.com/philopon/gulp-elm/tree/master/example), or
[devd](https://github.com/cortesi/devd).


### How does one render an HTML node conditionally?

Use `Html.text ""` as an empty element. E.g.

```haskell
if someCondition then
    Html.div [] [ {- ... some substantial Html value here ... -} ]
else
    Html.text ""
```

### Does Elm have ad-hoc polymorphism or typeclasses?

No. Elm provides three super-types that work like typeclasses:
`number`, `comparable`, and `appendable`. [^compappend]
The Elm language does not allow for defining our own super-types.

[^compappend]: There is also `compappend` in the compiler but it does not appear to be exposed for use.

The `number` type comprises Int and Float.

The `comparable` type comprises Int, Float, Char, String, List, and tuples[^tuple_compare].

[^tuple_compare]: Tuple comparison is supported only for tuples with 6 or fewer entries.

The `appendable` type comprises String, List, and text[^text_append].

[^text_append]: The compiler error messages refer to "text" being appendable; not sure what "text" means though. [FCY]

When declaring types the names "number", and "comparable" and "appendable" serve as prefixes. For example, a function declared as

```haskell
f : number -> number -> String
```

means that `f` can take two Int values and return a String, or two Float values and return a String. It cannot take a combination of Int and Float though; the actual types have to be consistent.

However, if declared as

```haskell
f : number1 -> number2 -> String
```

then it can also take an Int and a Float and return a String, or take a Float and an Int and return a String. The prefix gives the super-type that constrains the corresponding particular argument. The full super-type name, special prefix plus arbitrary (optional) suffix, determines whether the actual types have to be consistent.

### Which special type variables are there, and how do they work?

There are four special type vairables, which are `number`, `comparable`, `appendable`, and `compappend`. Please see question [Does Elm have ad-hoc polymorphism or typeclasses?](#does-elm-have-ad-hoc-polymorphism-or-typeclasses) for the details.

These built-in type variables work differently from the ones defined by user defined. The main difference is these built-in type variables can only mean what it defined to mean. For example:

```haskell
type alias PlusFn t =
  { fn: t -> t -> t
  }
```

It defines a type alias `PlusFn` which has one field `fn` which is a function that accepts two parameters which are of type `t`, and return a value of the same type. So, we can define any function which take two arguments and return one value, as long as they are of the same type, as the value of the `fn` field.

```haskell
> PlusFn (\x y -> x + y)
{ fn = <function> } : Repl.PlusFn number
> PlusFn (\x y -> x ++ y)
{ fn = <function> } : Repl.PlusFn appendable
```

However, if we change the name `t` to any built-in type variable, it will work differently. Let's say we change `t` to `number`, then `fn` must operate on `number`, i.e. either `Int` or `Float`. Since `Int` and `Float` don't support `(++)` operation, so the following code will not be valid any more:

```haskell
> PlusFn (\x y -> x ++ y)
-- TYPE MISMATCH --------------------------------------------- repl-temp-000.elm

The argument to function `PlusFn` is causing a mismatch.

4|   PlusFn (\x y -> x ++ y)
             ^^^^^^^^^^^^^^
Function `PlusFn` is expecting the argument to be:

    number -> number -> number

But it is:

    appendable -> appendable -> appendable
```

### How can I change the value of a field in a record?

You can't. Record values (like all Elm values) are immutable. However, you can
*copy* a record value and change one or more of its field values while doing so. Here is an example in elm-repl:

```
> x = { name = "Chris", age = 30 }
{ name = "Chris", age = 30 } : { name : String, age : number }
> y = { x | age = x.age + 1 }
{ name = "Chris", age = 31 } : { name : String, age : number }
> y
{ name = "Chris", age = 31 } : { name : String, age : number }
> x
{ name = "Chris", age = 30 } : { name : String, age : number }
```

## Footnotes

