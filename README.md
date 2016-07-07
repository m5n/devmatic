# devmatic: the automated (web) developer
At a fundamental level, computers respond to events. What if, given a precise description of events, a computer program could be generated automatically? Implementation frameworks come and go, but a description of how a system should react to events is a lot more static. In other words, this project hopes to capture everything I know and is an attempt to make myself obsolete!

## Goal
Go beyond scaffolding and prototyping and generate production-ready source code from an event description. Internationalization, accessibility, unit tests, everything. Users of this project would likely fill in some more JS details (by extension--no edits to the generated code of course) and provide CSS for the proper UI layout.

## Approach
1. Draw picture of UI
1. Group related sections
1. Identify the objects used in the UI
1. Use object names in the event definition

## Syntax
Syntax = &lt;command> &lt;object> on &lt;input> &lt;event>

&lt;command> = [cancel] edit | create | destroy [all] | show [all] | set [all]
- [cancel] edit: changes an object presentation from normal to edit mode (or vice versa if "cancel edit" is used)
- create: create a new item (regardless if it already has an id; no built-in smarts)
- destroy [all]: destroy one or all objects
- destroy all ... with &lt;key=value>[,&lt;key=value>]*: destroy only objects with the given property value(s)
  <br>mnemonic: destroy all products with an expiration date over a year ago
- show [all]: show one or all objects
- show all ... with &lt;key=value>[,&lt;key=value>]*: show only objects with the given property value(s)
  <br>mnemonic: show all students with a gpa of 8.0
- set [all] ... &lt;key=value>[,&lt;key=value>]*: set the given property value(s) for one or all objects
  <br>mnemonic: set car color to blue on paint button click
- set [all] ... &lt;key>: set the given property to the current value of an input type for one or all objects
  <br>mnemonic: set car color on paint selection change (applies selected value as property value)

&lt;object> = user defined

&lt;input> = [&lt;selector>] [&lt;input-type>]
- Not specific: do something on click
- More specific: do something on button click
- Even more specific: do something on save button click

&lt;event> = &lt;key> keypress | click | doubleclick | ...

&lt;input-type> = checkbox | ...
- checkbox triggers "name:checked:true" or "name:change(checked: true|false)"
- but only if there's a change listener registered for this checkbox
- Idea: perhaps shouldn't generate custom events... just hook things up directly

&lt;key> = any single keyboard key or multi-key combination, e.g. "p" or "esc" or "ctrl-c"

## To-do Application Example

See any of the examples on http://todomvc.com/ for how the UI behaves.

```
set all todos completed on toggle all checkbox change
==> $('checkbox.toggle-all').on('change', function (event) { todoItems.set({completed: $(event.target).prop('checked')}) })
    (Note the property name is "completed" for readability. Using "done" doesn't read well: set all todos done on toggle all checkbox change.)

edit todo on input doubleclick
==> $('.todo input').on('doubleclick', function (event) { $(event.target).closest('.todo').addClass('edit') })

cancel edit todo on input esc keypress
==> $('.todo input').on('keypress', function (event) { if (event.keyCode == Esc) { $(event.target).closest('.todo').removeClass('edit') }})

create todo on input enter keypress
==> $('.todo input').on('keypress', function (event) { if (event.keyCode == Enter) { 1. save new todo, 2. add to set on success }})

create todo on input blur
==> $('.todo input').on('blur', function () { 1. save new todo, 2. add to set on success })

set todo completed on checkbox change
==> $('.todo checkbox').on('change', function (event) { todo.set({completed: $(event.target).prop('checked')}) })

destroy todo on remove button click
==> $('.todo button.remove').on('click', function () { todo.destroy() })

show all todos on show all button click
==> $('button.show-all').on('click', function () { todoItems.filter({}) })

show all todos with completed=false on show active button click
==> $('button.show-active').on('click', function () { todoItems.filter({completed: false}) })

show all todos with completed=true on show completed button click
==> $('button.show-completed').on('click', function () { todoItems.filter({completed: true}) })

destroy all todos with completed=true on clear completed button click
==> $('button.clear-completed').on('click', function () { todoItems.destroy({completed: true}) })
```

## Roadmap
1. Define a DSL to describe events
  1. Make sure non-UI programmers can provide this event description (e.g. user experience or server-side folks)
  2. Ideally not browser-specific
1. Create a proof-of-concept to make sure there are no obvious gotchas preventing completion of this project
1. Write a parser to turn the DSL into source code
  1. JS objects and logic
  1. HTML needed to support the JS events
  1. CSS for some basic effects (e.g. busy states, validation)
1. Enhance the DSL to include Backend API calls
  1. Provide mock data in JS layer for easy testing
  1. Generate server-side code for a simple web server
1. Add ability to generate code for specific frameworks (e.g. Angular, Backbone)
1. Add ability to generate code for non-browsers, e.g. Andoid, iOS
1. Decide whether to stick with generated code or to write a browser plugin or to compile directy into browser source code
1. Expore use for non-HTTP applications (e.g. machine controllers, monitoring systems, expert systems)

## Decisions
* Terminology: use create/destroy (vs add/remove, insert/delete, save/delete)
  (consider add / remove (not necessarily destroy) vs.
  insert (in a specific spot) / delete (not necessarily destroy) vs.
  create (not necessarily add) / destroy... 
  here: an item cannot be separate from a set, so use create/destroy and manage add/remove internally)
* pick shortest word where possible (show vs filter, set vs update) ("add" was not chosen because of the above point)
* &lt;input> not tied to &lt;object> for plular &lt;object> usage (to prevent ".object-list button.global-action", etc)
* listen for "change"s rather than checked:true|false, for non-single-state controls 
  this also removes the need to differentiate between flip state vs all the same state as "all" box, e.g. toggle(state) = force state, toggle() = flip
* make singular/plural explicit via "all" (don't try to derive it from the user's object naming)
  TODO: so should &lt;object> always be singular? ("update all todo" vs "update all todos")
  mnemonic: "update all todo completed properTIES" and "update todo completed properTY"
* code gen: singular &lt;object> usage, so &lt;input> is somewhere within .object-name --> $('.object .selector')
* code gen: no singular &lt;object> usage, so &lt;input> not tied to object (no ties to .todo-list ever as &lt;li> cannot contain arbitrary markup such as buttons!) --> NOT $('.object-list .selector') but just $('.selector')

## Ideas
* syntax can be localized: syntax -> coding abstraction -> actual code
* &lt;input-type>s can be defined by user (HTML templates), so user can create custom input types, e.g. absolute-number-input using validator regexes
* desktop browser button receives "click", but mobile browser receives "tap"... abstract this concept in the syntax, e.g. "activate a control"?
  button = single-state control, checkbox = dual-state control or tri-state control, select = single-state or multi-state control

## TODOs
* add/remove "hover" class automatically to &lt;object> usages on mouseover/out
