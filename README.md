# <img src="http://elm-lang.org/logo.svg" width="26"> Elm Cheat Sheet

## Table of Contents
1. [Hello World](#hello-world)
2. [Tools](#tools)
    * [REPL](#repl)
    * [Compile](#compile)
    * [Packaging](#packaging)
    * [Publish](#publish)
3. [Modules](#modules)
    * [Imports](#imports)
4. [HTML Embedding](#html-embedding)
5. [Primitives](#primitives)
    * [Numbers](#numbers)
    * [Strings](#strings)
    * [Booleans](#booleans)
    * [Other](#other)
6. [Collections](#collections)
    * [Lists](#lists)
    * [Tuples](#tuples)
    * [Records](#records)
7. [Functions](#functions)
    * [Anonymous](#anonymous)
8. [Type Annotation](#type-annotation)
9. [Operators](#operators)
    * [Arithmetic](#arithmetic)
    * [Bitwise](#bitwise)
    * [Comparison](#comparison)
    * [Logical](#logical)
    * [Function Composition](#function-composition)
    * [Other](#other)
10. [Control Statements](#control-statements)
    * [If](#if)     

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

main =
  h1 [id "hw"] [text "Hello World!"]
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
elm make HelloWorld.elm -> elm.js

# Custom name
$ elm make HelloWorld.elm --output hw.js

# With warnings
$ elm make HelloWorld.elm --warn

# To HTML
$ elm make HelloWorld.elm --output hw.html
```

#### Packaging
`elm package` / `elm-package`

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

#### Publish
Versioning matters.<br/>
`MAJOR.MINOR.PATCH`
* `PATCH` - the API is the same, no risk of breaking code
* `MINOR` - values have been added, existing values are unchanged
* `MAJOR` - existing values have been changed or removed

`elm-package.json`
* `source-directories` - array of directories to look up for project source code inside the working directory
* `exposed-modules` - array of directories to expose publishing modules that are not meant for users

Publishing a package requires well documented code.

```bash
git tag -a 1.0.0 -m "initial release"
git push --tags

elm-package publish
```

Update a package
```bash
elm-package bump
```

## Modules
```elm
-- Defining a module, exports everything by default
module Mymodule where

-- Export only specified entities
module Mymodule (Type, value) where

-- Export all or specific states of type
module Mymodule 
    ( Error(Forbidden, Timeout)
    , Stuff(..)
    ) where
    
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

## HTML Embedding
Running fullscreen
`elm-make HelloWorld.elm` -> `elm.js`
```html
<script type="text/javascript" src="elm.js"></script>
<script type="text/javascript">
    Elm.fullscreen(Elm.HelloWorld);
</script>
```

Embed explicitly in a html element
```html
<script type="text/javascript" src="elm.js"></script>
<script type="text/javascript">
    var elmHolder = document.getElementById('hw-wrapper');
    
    Elm.embed(Elm.HelloWorld, elmHolder);
</script>
```

Run without graphics
```javascript
Elm.worker(Elm.HelloWorld);
```

## Primitives
#### Numbers
Numeric types are `number` and `Float`, `number` represents both `Int` and `Float`:
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
String types are `char` and `String`
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
> [1..4]
> [1,2,3,4]
> 1 :: [2,3,4]
> 1 :: 2 :: 3 :: 4 :: []
```

#### Tuples
Tuples package two or more expressions into a single expression. 
The type of a tuple records the number of components and each of their types.
```elm
> (1, "2", True)
(1,"2",True) : ( number, String, Bool )
```

Also possible, put as many commas as you'll have values inside a tuple
```elm
> (,,,) 1 True 'a' []
(1,True,'a',[]) : ( number, Bool, Char, List a )
```

#### Records
Records are immutable. A `record` is a collection of key/value pairs, 
similar to objects in JavaScript or dictionary in Python
```elm
myRecord = 
 { style = "Blue",
   number = 1, 
   isCool = True 
 }

-- Accessing records
> myRecord.style
"Blue" : String
> .style myRecord
"Blue" : String

-- Updating records returns a new record
> updatedRecord = { myRecord | style = "Red" }
> myRecord.style
"Blue" : String
> updatedRecord.style
"Red" : String
```

## Functions
All functions in Elm are curried by default.
The function arguments are separated from the function name by a single space. 
And function arguments are separated by a single space, too.
```elm
-- function name | arguments names = function body
sum a b = a + b

-- Partial application
> minus x y = (-) x y
<function> : number -> number -> number
> minus1 = minus 1
<function> : number -> number
> minus 11
-10 : number
```

#### Anonymous
```elm
-- function arguments -> function body
(\n -> n < 0)
(\x y -> x * y)
```

## Type Annotation
Elm, like most ML dialects, automatically infers most types.
```elm
-- function name : 1st arg type -> 2nd arg type -> return type
fnc : Int -> List -> Int
```
Example below is read as function that takes an __a__ value and returns a __b__ value, list of __a__ values returns a list of __b__ values
```elm
map: (a -> b) -> List a -> List b
```

## Operators
In a nutshell Elm operators are _functions_.

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
|`||`|logical or|`Bool -> Bool -> Bool`
|`not`|logical not|`Bool`

#### Function Composition
|Operator|Description|Type hint|
|--------|-----------|----------|
|`<|`|backward (pipe) function application `f <| x == f x`|`(a -> b) -> a -> b`
|`|>`|forward (pipe) function application `x |> f == f x`|`a -> (a -> b) -> b`
|`<<`|composes functions into one, arguments first applied to the function from the right side|`(b -> c) -> (a -> b) -> a -> c`
|`>>`|same as before except arguments first applied to the function from the left side|`(a -> b) -> (b -> c) -> a -> c`

#### Other
|Operator|Description|Type hints|
|--------|-----------|----------|
|`++`|put appendable things together|`appendable -> appendable -> appendable`|
|`::`|add an element to the front of a list|`a -> List a -> List a`|

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


