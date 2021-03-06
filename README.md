# CoffeeScript Style Guide

This guide presents a collection of best-practices and coding conventions for the [CoffeeScript][coffeescript] programming language.

## Table of Contents

* [The CoffeeScript Style Guide](#guide)
    * [Example](#example)
    * [Code Layout](#code_layout)
        * [Tabs or Spaces?](#tabs_or_spaces)
        * [Maximum Line Length](#maximum_line_length)
        * [Blank Lines](#blank_lines)
        * [Trailing Whitespace](#trailing_whitespace)
        * [Optional Commas](#optional_commas)
        * [Encoding](#encoding)
    * [Module Imports](#module_imports)
    * [Whitespace in Expressions and Statements](#whitespace)
    * [Comments](#comments)
        * [Block Comments](#block_comments)
        * [Inline Comments](#inline_comments)
    * [Naming Conventions](#naming_conventions)
    * [Functions](#functions)
    * [Strings](#strings)
    * [Conditionals](#conditionals)
    * [Looping and Comprehensions](#looping_and_comprehensions)
    * [Extending Native Objects](#extending_native_objects)
    * [Exceptions](#exceptions)
    * [Annotations](#annotations)
    * [Miscellaneous](#miscellaneous)

<a name="example"/>
## Example
```coffeescript
'use strict'

TimelineController = (TimelineService, $stateParams) ->
  vm                  = this
  vm.coPilotHandle    = null
  vm.members          = []
  vm.avatars          = {}
  vm.submissionHandle = null
  vm.feedbackHandle   = null
  vm.feedback2Handle  = null

  mapEvents = [
    { key: 'submitted', value: 'submitted' }
    { key: 'email', value: 'email-verified' }
    { key: 'quote', value: 'quote-created' }
    { key: 'payment', value: 'payment-accepted' }
    { key: 'coPilot', value: 'copilot-assigned' }
    { key: 'launched', value: 'launched' }
    { key: 'joined', value: 'challenge-member-registered' }
    { key: 'submissions', value: 'challenge-submission' }
    { key: 'feedback', value: 'challenge-feedback-provided' }
    { key: 'checkpoint1', value: 'checkpoint1' }
    { key: 'finalists', value: 'finalists' }
    { key: 'finalistsSelected', value: 'challenge-finalists-selected' }
    { key: 'finalDesign', value: 'final-design' }
    { key: 'winner', value: 'winner' }
    { key: 'finalFeedback', value: 'final-feedback' }
    { key: 'completed', value: 'completed' }
  ]

  activate = ->
    for mapEvent in mapEvents
      vm[mapEvent.key] =
        passed   : false
        completed: false

    params =
      workId: $stateParams.workId

    TimelineService.getEvents params, onChange

  onChange = (timeline) ->
    setStatus timeline

    vm.coPilotHandle    = timeline.coPilot
    vm.members          = timeline.members
    vm.avatars          = timeline.avatars
    vm.submissionHandle = timeline.submission
    vm.submissionThumbs = timeline.submissionThumbs
    vm.feedbackHandle   = timeline.feedback
    vm.feedback2Handle  = timeline.feedback2

  setStatus = (timeline) ->
    for mapEvent in mapEvents
      vm[mapEvent.key].completed = timeline.createdDates?[mapEvent.value]

    for mapEvent, i in mapEvents
      if mapEvents[i + 1]
        vm[mapEvent.key].passed = vm[mapEvents[i + 1].key].completed

  activate()

  vm

TimelineController.$inject = [
  'TimelineService'
  '$stateParams'
]

angular.module('appirio-tech-timeline').controller 'TimelineController', TimelineController

```

<a name="code_layout"/>
## Code layout

<a name="tabs_or_spaces"/>
### Tabs or Spaces?

Use **spaces only**, with **2 spaces** per indentation level. Never mix tabs and spaces.

<a name="maximum_line_length"/>
### Maximum Line Length

Limit all lines to a maximum of 79 characters.

<a name="blank_lines"/>
### Blank Lines

No blank line for the first indentation in any case.

Blank line after unindenting.

Blank line before and after keyword blocks: if, unless, for

There should never be two consecutive blank lines.

<a name="trailing_whitespace"/>
### Trailing Whitespace

Do not include trailing whitespace on any lines.

<a name="optional_commas"/>
### Optional Commas

Avoid the use of commas before newlines when properties or elements of an Object or Array are listed on separate lines.

```coffeescript
# Yes
foo = [
  'some'
  'string'
  'values'
]
bar:
  label: 'test'
  value: 87

# No
foo = [
  'some',
  'string',
  'values'
]
bar:
  label: 'test',
  value: 87
```

<a name="encoding"/>
### Encoding

UTF-8 is the preferred source file encoding.

<a name="module_imports"/>
## Module Imports

If using a module system (CommonJS Modules, AMD, etc.), `require` statements should be placed on separate lines.

```coffeescript
require 'lib/setup'
Backbone = require 'backbone'
```
These statements should be grouped in the following order:

1. Standard library imports _(if a standard library exists)_
2. Third party library imports
3. Local imports _(imports specific to this application or library)_

<a name="whitespace"/>
## Whitespace in Expressions and Statements

Avoid extraneous whitespace in the following situations:

- Immediately inside parentheses, brackets or braces

    ```coffeescript
       ($ 'body') # Yes
       ( $ 'body' ) # No
    ```

- Immediately before a comma

    ```coffeescript
       console.log x, y # Yes
       console.log x , y # No
    ```

Additional recommendations:

- Always surround these binary operators with a **single space** on either side

    - assignment: `=`

        - _Note that this also applies when indicating default parameter value(s) in a function declaration_

           ```coffeescript
           test: (param = null) -> # Yes
           test: (param=null) -> # No
           ```

    - augmented assignment: `+=`, `-=`, etc.
    - comparisons: `==`, `<`, `>`, `<=`, `>=`, `unless`, etc.
    - arithmetic operators: `+`, `-`, `*`, `/`, etc.


<a name="comments"/>
## Comments

If modifying code that is described by an existing comment, update the comment such that it accurately reflects the new code. (Ideally, improve the code to obviate the need for the comment, and delete the comment entirely.)

The first word of the comment should be capitalized, unless the first word is an identifier that begins with a lower-case letter.

If a comment is short, the period at the end can be omitted.

<a name="block_comments"/>
### Block Comments

Block comments apply to the block of code that follows them.

Each line of a block comment starts with a `#` and a single space, and should be indented at the same level of the code that it describes.

Paragraphs inside of block comments are separated by a line containing a single `#`.

```coffeescript
  # This is a block comment. Note that if this were a real block
  # comment, we would actually be describing the proceeding code.
  #
  # This is the second paragraph of the same block comment. Note
  # that this paragraph was separated from the previous paragraph
  # by a line containing a single comment character.

  init()
  start()
  stop()
```

<a name="inline_comments"/>
### Inline Comments

Inline comments are placed on the line immediately above the statement that they are describing. If the inline comment is sufficiently short, it can be placed on the same line as the statement (separated by a single space from the end of the statement).

All inline comments should start with a `#` and a single space.

The use of inline comments should be limited, because their existence is typically a sign of a code smell.

Do not use inline comments when they state the obvious:

```coffeescript
  # No
  x = x + 1 # Increment x
```

However, inline comments can be useful in certain scenarios:

```coffeescript
  # Yes
  x = x + 1 # Compensate for border
```

<a name="naming_conventions"/>
## Naming Conventions

Use `camelCase` (with a leading lowercase character) to name all variables, methods, and object properties.

Use `CamelCase` (with a leading uppercase character) to name all classes. _(This style is also commonly referred to as `PascalCase`, `CamelCaps`, or `CapWords`, among [other alternatives][camel-case-variations].)_

_(The **official** CoffeeScript convention is camelcase, because this simplifies interoperability with JavaScript. For more on this decision, see [here][coffeescript-issue-425].)_

For constants, use all uppercase with underscores:

```coffeescript
CONSTANT_LIKE_THIS
```

<a name="functions"/>
## Functions

_(These guidelines also apply to the methods of a class.)_

When declaring a function that takes arguments, always use a single space after the closing parenthesis of the arguments list:

```coffeescript
foo = (arg1, arg2) -> # Yes
foo = (arg1, arg2)-> # No
```

Do not use parentheses when declaring functions that take no arguments:

```coffeescript
bar = -> # Yes
bar = () -> # No
```

In cases where method calls are being chained and the code does not fit on a single line, each call should be placed on a separate line and indented by one level (i.e., two spaces), with a leading `.`.

```coffeescript
[1..3]
  .map((x) -> x * x)
  .concat([10..12])
  .filter((x) -> x < 11)
  .reduce((x, y) -> x + y)
```

Some time its better to avoid chaining in favor for readability.
```coffeescript
queryParams =
  filter: 'sourceObjectId=' + params.workId

resource = TimelineAPIService.query queryParams

resource.$promise.then (response) ->
  for item in resource
    item.sourceObjectContent.handle += '1'

  buildTimeline response, onChange

resource.$promise.catch ->
  #TODO: handle error

resource.$promise.finally ->
  #TODO: handle finally
```

When calling functions, choose to omit or include parentheses in such a way that optimizes for readability. Keeping in mind that "readability" can be subjective, the following examples demonstrate cases where parentheses have been omitted or included in a manner that the community deems to be optimal:

```coffeescript
baz 12

foo(4).bar(8)

obj.value(10, 20) / obj.value(20, 10)

print inspect value

new Tag(new Value(a, b), new Arg(c))
```

Create meaningful variables when using object/array as arguments.  This will help explain the code and reduce confusion with coffee conversion.
```coffeescript
# Yes
coordinates =
  x: 10
  y: 20

brush.ellipse coordinates

# No
brush.ellipse x: 10, y: 20

# No
brush.ellipse
  x: 10
  y: 20
```

You will sometimes see parentheses used to group functions (instead of being used to group function parameters). Examples of using this style (hereafter referred to as the "function grouping style"):

```coffeescript
($ '#selektor').addClass 'klass'

(foo 4).bar 8
```

This is in contrast to:

```coffeescript
$('#selektor').addClass 'klass'

foo(4).bar 8
```

In cases where method calls are being chained, some adopters of this style prefer to use function grouping for the initial call only:

```coffeescript
($ '#selektor').addClass('klass').hide() # Initial call only
(($ '#selektor').addClass 'klass').hide() # All calls
```

The function grouping style is not recommended. However, **if the function grouping style is adopted for a particular project, be consistent with its usage.**

<a name="strings"/>
## Strings

Dont use string interpolation instead of string concatenation:

```coffeescript
"this is an #{adjective} string" # No
'this is an ' + adjective + ' string' # Yes
```

Prefer single quoted strings (`''`) instead of double quoted (`""`) strings.

<a name="conditionals"/>
## Conditionals

Favor `unless` over `if` for negative conditions.

Instead of using `unless...else`, use `if...else`:

```coffeescript
  # Yes
  if true
    ...
  else
    ...

  # No
  unless false
    ...
  else
    ...
```

Multi-line if/else clauses should use indentation:

```coffeescript
  # Yes
  if true
    ...
  else
    ...

  # No
  if true then ...
  else ...
```

<a name="looping_and_comprehensions"/>
## Looping and Comprehensions

Avoid complex comprehensions:

```coffeescript
  # No
  result = (item.name for item in array)

  # Yes
  results = []
  for item in array
    results.push item.name
```

```coffeescript
# No
result = (item for item in array when item.name is "test")
```
```coffeescript
# No
object = one: 1, two: 2
alert("#{key} = #{value}") for key, value of object
```

<a name="extending_native_objects"/>
## Extending Native Objects

Do not modify native objects.

For example, do not modify `Array.prototype` to introduce `Array#forEach`.

<a name="exceptions"/>
## Exceptions

Do not suppress exceptions.

<a name="annotations"/>
## Annotations

Use annotations when necessary to describe a specific action that must be taken against the indicated block of code.

Write the annotation on the line immediately above the code that the annotation is describing.

The annotation keyword should be followed by a colon and a space, and a descriptive note.

```coffeescript
  # FIXME: The client's current state should *not* affect payload processing.
  resetClientState()
  processPayload()
```

If multiple lines are required by the description, indent subsequent lines with two spaces:

```coffeescript
  # TODO: Ensure that the value returned by this call falls within a certain
  #   range, or throw an exception.
  analyze()
```

Annotation types:

- `TODO`: describe missing functionality that should be added at a later date
- `FIXME`: describe broken code that must be fixed
- `OPTIMIZE`: describe code that is inefficient and may become a bottleneck
- `HACK`: describe the use of a questionable (or ingenious) coding practice
- `REVIEW`: describe code that should be reviewed to confirm implementation

If a custom annotation is required, the annotation should be documented in the project's README.

<a name="miscellaneous"/>
## Miscellaneous

`&&` is preferred over and``.

`||` is preferred over `or`.

`==` is preferred over `is`.

`!=` is preferred over `isnt`.

`!` is preferred over `not`.

`||=` should be used when possible:

```coffeescript
temp or= {} # No
temp = temp || {} # Yes
```

Dont use shorthand notation (`::`) for accessing an object's prototype:

```coffeescript
Array::slice # No
Array.prototype.slice # Yes
```

Prefer `@property` over `this.property`.

```coffeescript
return @property # Yes
return this.property # No
```

However, avoid the use of **standalone** `@`:

```coffeescript
return this # Yes
return @ # No
```

Avoid `return` where not required, unless the explicit return increases clarity.

Dont use splats (`...`) when working with functions that accept variable numbers of arguments:

```coffeescript
console.log args... # No

(a, b, c, rest...) -> # No
```

