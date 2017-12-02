# <img src="http://elm-lang.org/assets/logo.svg" width="26"> Elm Cheat Sheet

## Table of Contents
1. [Hello World](#hello-world)
2. [Comments](#comments)
3. [Modules](#modules)
    * [Imports](#imports)
4. [Tools](#tools)
    * [REPL](#repl)
    * [Compile](#compile)
    * [Packaging](#packaging)
        * [Publish](#publish)
        * [Documentation](#documentation)
5. [HTML Embedding](#html-embedding)
6. [Primitives](#primitives)
    * [Numbers](#numbers)
    * [Strings](#strings)
    * [Booleans](#booleans)
    * [Other](#other)
7. [Collections](#collections)
    * [Lists](#lists)
    * [Tuples](#tuples)
    * [Records](#records)
    * [Other](#other)
8. [Functions](#functions)
    * [Anonymous](#anonymous)
    * [Prefix](#prefix)
9. [Types](#types)
    * [Union Types](#union-types)
    * [Maybe](#maybe)
10. [Type Aliases](#type-aliases)
11. [Type Annotation](#type-annotation)
12. [Operators](#operators)
    * [Arithmetic](#arithmetic)
    * [Bitwise](#bitwise)
    * [Comparison](#comparison)
    * [Logical](#logical)
    * [Function Composition](#function-composition)
    * [Other](#other)
13. [Control Statements](#control-statements)
    * [If](#if)
    * [Case-of](#case-of)
    * [Let-in](#let-in)
14. [Ports](#ports)

## Elm in a nutshell
 - purely functional language
 - statically typed
 - no runtime exceptions
 - outperforms most popular rendering libraries
 - package manager
 - built-in tooling
 - HTML, CSS, JavaScript interoperability
 - clean syntax
 - I like frontend again...

## Hello World
File `HelloWorld.elm`:
```elm
import Html exposing (h1, text)
import Html.Attributes exposing (id)

-- Hello world example
main =
  h1 [id "hw"] [text "Hello World!"]
```

## Comments
```elm
-- Single line comment

{-
Multi-line comment
-}
```

Comments for package [documentation](#documentation)

## Modules
```elm
-- Defining a module that exports everything
module Mymodule exposing (..)

-- Export only specified entities
module Mymodule exposing (Type, value)

-- Export all or specific states of type
module Mymodule exposing
    ( Error(Forbidden, Timeout)
    , Stuff(..)
    )

type Error
    = Forbidden String
    | Timeout String
    | NotFound String
```

#### Imports
```elm
-- qualified imports
import String                       -- String.toUpper, String.repeat
import String as Str                -- Str.toUpper, Str.repeat

-- unqualified imports
import Mymodule exposing (..)                 -- Error, Stuff
import Mymodule exposing ( Error )            -- Error
import Mymodule exposing ( Error(..) )        -- Error, Forbidden, Timeout
import Mymodule exposing ( Error(Forbidden) ) -- Error, Forbidden
```


## Tools
#### REPL
`elm repl` / `elm-repl`

Import required modules
```elm
> import List
```

Get function signature
```elm
> List.map
<function> : (a -> b) -> List a -> List b
> (++)
<function> : appendable -> appendable -> appendable
```

Elm expressions return resulting value and type
```elm
> 1 + 1
2 : number
```

Backslash `\` is for multi-line expressions
```
> fn a b = \
|    a + b
<function> : number -> number -> number
```
 
#### Compile
`elm make` / `elm-make`

```bash
# Default compilation 
elm make HelloWorld.elm -> index.html

# Custom name
$ elm make HelloWorld.elm --output hw.js

# Multiple files
$ elm make HelloWorld.elm MyModule.elm --output hw.js

# With warnings
$ elm make HelloWorld.elm --warn

# To HTML
$ elm make HelloWorld.elm --output hw.html
```

#### Packaging
`elm package` / `elm-package`

Installation automatically adds `dependencies` in `package.json`.
```bash
# Install a package
$ elm-package install evancz/elm-html

# Specific version
$ elm-package install evancz/elm-html 1.0.0

# Diff two versions
$ elm-package diff evancz/virtual-dom  2.0.0 2.1.0
Comparing evancz/virtual-dom 2.0.0 to 2.1.0...
This is a MINOR change.

------ Changes to module VirtualDom - MINOR ------

    Added:
        attributeNS : String -> String -> String -> VirtualDom.Property
```

#### Documentation
Publishing a package requires well documented code.

```elm
module Documentation exposing
    ( Type
    , value
    , anyfinCanHappen
    )

{-| Module level documentation

# Just a header, what to include below
@docs Type, value

# About Anyfin
@docs anyfinCanHappen

-}

import Random exposing (int)

{-| Type level documentation comment -}
type Type = Bool

{-|-}
-- Empty comment above
value : Int
value = 1 + 1

{-| More on anyfinCanHappen -}
anyfinCanHappen : Generator Int
anyfinCanHappen =
    int 0 64

{-| This value is not exported, so isn't required -}
-- Use basic comment syntax
imNotExported =
    "Don't need to comment me"
```

* Documentation comment starts `{-|` ends with `-}`
* Module documentation comes after module declaration, before the imports
* Functions are grouped into related sections by keyword `@docs <args>` and declared with Markdown
* Each exported entity should have documentation comment on top of its declaration

#### Publish
Add `README.md` otherwise publishing will fail<br/>
All packages start with initial version `1.0.0`<br/>
Versions all have exactly three parts: `MAJOR.MINOR.PATCH`<br/>
* `PATCH` - the API is the same, no risk of breaking code
* `MINOR` - values have been added, existing values are unchanged
* `MAJOR` - existing values have been changed or removed

`elm-package.json`
* `source-directories` - array of directories to look up for project source code inside the working directory
* `exposed-modules` - modules that exposed to a user after publishing, kind of interface to your internal API.

```bash
# elm-package.json
source-directories": [
    ".",
    "SubOne",
    "SubTwo"
]
"exposed-modules": [
    "SubOne",
    "Goodmodule",
    "Module"
]

├── SubOne
│   └── SubOne.elm        # Compiled and exposed
├── SubTwo
│   └── SubTwo.elm        # Compiled, but unexposed
├── SubThree
│   └── SubThree.elm      # Compiler won't see the source, unexposed to the users
├── README.md
├── elm-package.json
├── Goodmodule.elm        # Compiled and exposed
└── Module.elm            # Compiled and exposed
```

Publish
```bash
$ git tag -a 1.0.0 -m "initial release"
$ git push --tags

$ elm-package publish
```

Updating
```bash
$ elm-package bump

$ git tag -a 1.0.1 -m "secondary release"
$ git push --tags

$ elm-package publish
```

## HTML Embedding
Running fullscreen
`elm-make HelloWorld.elm` -> `elm.js`
```html
<script type="text/javascript" src="elm.js"></script>
<script type="text/javascript">
    Elm.Main.fullscreen();
</script>
```

Embed explicitly in a html element
```html
<script type="text/javascript" src="elm.js"></script>
<script type="text/javascript">
    var elmHolder = document.getElementById('hw-wrapper');

    Elm.Main.embed(elmHolder);
</script>
```

Run without graphics
```javascript
Elm.Main.worker();
```

## Primitives
#### Numbers
Numeric types are `Int` and `Float`, `number` represents both `Int` and `Float`:
```elm
> 1
1 : number
> 2.0
2 : Float
> truncate 0.1
0 : Int
> truncate 1
1 : Int
```

#### Strings
String types are `Char` and `String`
```elm
> 'a'
'a' : Char
> "Hello"
"Hello" : String
```

Multi-line string
```elm
"""
Hello
World
"""
```

Single quotes are for `char` only
```elm
> 'ab'
-- SYNTAX PROBLEM --
> "ab"
"ab" : String
```

#### Booleans
```elm
> True
True : Bool
> False
False : Bool
```

#### Other
`comparable` - `ints`, `floats`, `chars`, `strings`, `lists`, `tuples`
<br/>
`appendable` - `strings`, `lists`, `text`.
<br/>
Kind of dynamic types are represented as `a`, `b`, `c` etc. meaning that you can pass any value, even functions

## Collections
All values and data structures in Elm are immutable.

#### Lists
A `list` holds a collection of related values separated by commas and enclosed in
square brackets. All the values in a list must have the same type:
```elm
> []
[] : List a
> [1,2,3]
[1,2,3] : List number
> ["a", "b", "c"]
["a","b","c"] : List String
```

Ways to create a list
```elm
> List.range 1 4
> [1,2,3,4]
> 1 :: [2,3,4]
> 1 :: 2 :: 3 :: 4 :: []
```

#### Tuples
Tuples package many expressions into a single expression.
They have a minumum of two elements and maximum of nine.
The type of a tuple records the number of components and each of their types.
```elm
> (1, "2", True)
(1,"2",True) : ( number, String, Bool )
```

It's also possible to use commas as a tuple function, like a [prefix operation](#prefix).
```elm
> (,,,) 1 True 'a' []
(1,True,'a',[]) : ( number, Bool, Char, List a )
```

Destructuring
```elm
(x, y) = (1, 2)
> x
1 : number
```

#### Records
A `record` is a collection of key/value pairs,
similar to objects in JavaScript or dictionary in Python
```elm
myRecord =
 { style = "Blue",
   number = 1,
   isCool = True
 }
```

Accessing records
```elm
> myRecord.style
"Blue" : String
> .style myRecord
"Blue" : String
```

Updating records returns a new record
```elm
> updatedRecord = { myRecord | style = "Red", number = 10, isCool = False }
> myRecord.style
"Blue" : String
> updatedRecord.style
"Red" : String
```

Destructuring
```elm
{ style, number, isCool } = myRecord
> style
"Blue" : String
```

Extensible records

```elm
-- good usecase for extensible records is to be able to compose records and to be able to define functions that work on different kinds of records

-- define a type of record that has some properties and could have other properties

type alias Mammal r = {
   r | legs : Int
}

-- cat is an example of extending the record Mammal by adding properties name and ageInHumanYears

cat : Mammal
    { name : String
    , ageInHumanYears = 13
    }

cat =
  { name = "Mittens"
  , ageInHumanYears = 13
  , legs = 4
  }
```

```elm
-- mutate is a function that can work on any Mammal

mutate : Mammal r -> Mammal r
mutate mammal =
    { mammal | legs = mammal.legs + 1 }

mutatedCat = mutate cat
mutatedCat.legs == 5
```

#### Other
Core library also has:
 * [Array](http://package.elm-lang.org/packages/elm-lang/core/latest/Array)
 * [Dict](http://package.elm-lang.org/packages/elm-lang/core/latest/Dict)
 * [Set](http://package.elm-lang.org/packages/elm-lang/core/latest/Set)

## Functions
Basics
```elm
-- function name | arguments names = function body
sum a b = a + b

-- combine arguments in a tuple
sum (a, b) = a + b
```

All functions in Elm are _curried_ by default.<br/>
If you have "a function of 2 arguments", it's really a function that takes one argument and returns a function that takes another argument:
```elm
-- Both are equal
myFunction arg1 arg2
((myFunction arg1) arg2)

-- Partial application
> minus x y = (-) x y
<function> : number -> number -> number
> minus1 = minus 1
<function> : number -> number
> minus1 11
-10 : number
```

#### Anonymous
Also known as _lambdas_
```elm
-- (\function arguments -> function body)
-- parenthesized, content starts with backslash
(\n -> n < 0)
(\x y -> x * y)
```

#### Prefix
_Prefix_ notation is when we use [operators](#operators) as regular functions by enclosing them in parentheses.
```elm
-- Normally you would do this
> "abcde" ++ "fghij"
"abcdefghij" : String

-- Prefix
> (++) "abcde" "fghij"
"abcdefghij" : String
```

## Types
#### Union Types
Elm allows to create custom types known as _union types_.<br/>
The expression below creates a type which can have one of the values (or _tags_) from the right. _Union types_ tightly coupled with [case-of](#case-of) statement.
```elm
type Movement = Right | Left | Stop
```

Tags bring additional information, after tag itself comes a type or multiple types.
```elm
type Movement
    = Right Int
    | Left Int
    | Stop Bool
    | Coordinates (Float, Float)

-- passing to the function
myFunction ( Coordinates (45.7, 67.5) )
```

_Union types_ can also have type variables
```elm
type Person a
  = Name String
  | Surname String
  | Age Int
  | About a
```

#### Maybe
A `Maybe` can help you with optional arguments, error handling, and records with optional fields. Think of it as a kind of `null`
```elm
-- Maybe resides in a module
import Maybe exposing ( Maybe(..) )

-- Takes an argument that can be filled with any value
type Maybe a = Just a | Nothing
```

[Type annotation](#type-annotation) explicitly tells that it will give back an `Int` or it won't.
```elm
getId : Int -> Maybe Int
getId id =
  if id >= 0 then
    Just id
  else
    Nothing
```

## Type Aliases
You can give existing types a custom name with `type alias`
```elm
type alias Name = String
type alias Dob = String

type alias Record = { name: Name, dob: Dob }
```

We can use it later annotating function
```elm
record : Record
record =
    { name = "Dave", dob = "27/08/1999" }
```

But still `type alias` equals to it's parent `type`
```elm
type alias Name = String

name : Name
name =
  "Dave"

secondName : String
secondName =
  "Dave"

-- True
name == secondName
```

## Type Annotation
Elm, like most ML dialects, automatically infers most types.<br/>
```elm
-- function name : 1st arg type -> 2nd arg type -> return type
fnc : Int -> List -> Int
```
Example below is read as function that takes an __a__ value and returns a __b__ value, list of __a__ values returns a list of __b__ values
```elm
map: (a -> b) -> List a -> List b
```

Pattern matching on record fields
```elm
-- Requires the argument with x and y fields
multiply {x,y} =
    x * y
```

Annotating records
```elm
coordinates : { x : Float, y : Float }
coordinates =
    { x = 0,
      y = 0
    }
```

## Operators
In a nutshell Elm operators are _functions_ that take two arguments.

#### Arithmetic
|Operator|Description|Type hint|
|--------|-----------|----------|
|`+`|addition|`number -> number -> number`
|`-`|subtraction|`number -> number -> number`
|`*`|multiplication|`number -> number -> number`
|`/`|floating point division|`Float -> Float -> Float`
|`//`|integer division, discard the reminder|`Int -> Int -> Int`
|`^`|exponentiation|`number -> number -> number`
|`%`|modulo|`Int -> Int -> Int`

#### Bitwise
|Operator|Description|Type hint|
|--------|-----------|----------|
|`and`|bitwise AND|`Int -> Int -> Int`
|`or`|bitwise OR|`Int -> Int -> Int`
|`xor`|biwise XOR|`Int -> Int -> Int`

#### Comparison
|Operator|Description|Type hint|
|--------|-----------|----------|
|`==`|equal|`comparable -> comparable -> Bool`
|`/=`|not equal|`comparable -> comparable -> Bool`
|`<`|less than|`comparable -> comparable -> Bool`
|`<=`|less than or equal|`comparable -> comparable -> Bool`
|`>`|greater than|`comparable -> comparable -> Bool`
|`>=`|greater than or equal|`comparable -> comparable -> Bool`

#### Logical
|Operator|Description|Type hint|
|--------|-----------|----------|
|`&&`|logical and|`Bool -> Bool -> Bool`
|`\|\|`|logical or|`Bool -> Bool -> Bool`
|`not`|logical not|`Bool -> Bool`

#### Function Composition
|Operator|Description|Type hint|
|--------|-----------|----------|
|`<\|`|backward (pipe) function application `f <\| x == f x`|`(a -> b) -> a -> b`
|`\|>`|forward (pipe) function application `x \|> f == f x`|`a -> (a -> b) -> b`
|`<<`|composes functions into one, arguments first applied to the function from the right side|`(b -> c) -> (a -> b) -> a -> c`
|`>>`|same as before except arguments first applied to the function from the left side|`(a -> b) -> (b -> c) -> a -> c`

#### Other
|Operator|Description|Type hints|
|--------|-----------|----------|
|`++`|put appendable things together|`appendable -> appendable -> appendable`|
|`::`|add an element to the front of a list|`a -> List a -> List a`|
|`as`|keyword that creates aliases for values `(x, y) as t == t = (x, y)`|`a -> a`|

## Control statements
#### If
All the branches of an if need to match so that no matter which one we take, we get back the same type of value overall.
```elm
if a < 1 then
    "It's zero"
else
    "Non-zero"

-- Multi-line.
if y > 0 then
    "Greater"
else if x /= 0 then
    "Not equals"
else
    "silence"
```

Elm does not have the notion of “truthiness”.<br/>
The condition must evaluate to True or False, and nothing else.
```elm
> if 1 then "nope" else "nope again"
- TYPE MISMATCH --
```

#### Case-of
Case tries to match the value of type against patterns defined after the `of` keyword
```elm
type User
    = Activated
    | Deleted

update state =
  case state of
    Activated ->
      -- do something
    Deleted ->
      -- do again
```

In case of passing tags with additional properties, parameters are passed along with type checking
```elm
type User
    = Activated Int
    | Deleted (Int, String)

update state =
  case state of
    Activated value ->
      -- do something with value
    Deleted values ->
      -- do something with values

update ( Activated 1 )
update ( Deleted (0, "gone") )
```

#### Let-in
`let` allows you to define intermediate values.
```elm
let
  x = 3 * 8
  y = 4 ^ 2
in
  x + y
```

`let` helps simplify complex expressions
```elm
let
  activeUsers = List.filter (\u -> u.state /= 1) model.users
in
  { model | user = activeUsers}
```

## Ports
Ports are a general purpose way to communicate with JavaScript.

#### From JavaScript to Elm
```elm
-- declare that this module uses ports
port module Main exposing (..)

-- define port
port portName : (String -> msg) -> Sub msg
```

```javascript
var main = Elm.Main.embed(div);

// send into port
main.ports.portName.send("Port value");
```

#### From Elm to JavaScript
```elm
port showPortName : String -> Cmd msg
```

```javascript
function logName(name) {
    console.log(name);
}

// subscribe to receive events
main.ports.showPortName.subscribe(logName);

// unsubscribe
main.ports.showPortName.unsubscribe(logName);
```

#### Type interoperability
|JavaScript|Elm|
|----------|---|
|Booleans|Bool|
|Numbers|Int, Float|
|Strings|Char, String|
|Arrays|List, Array, Tuples (fixed-length)|
|Objects|Records|
|JSON|[Json.Encode.Value](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Encode#Value)|
|null|Maybe Nothing|
