# devmatic: the automated (web) developer
At a fundamental level, computers respond to events. What if, given a precise description of events, a computer program could be generated automatically? Implementation frameworks come and go, but a description of how a system should react to events is a lot more static. In other words, this project hopes to capture everything I know and is an attempt to make myself obsolete!

## Goal
Go beyond scaffolding and prototyping and generate production-ready source code from an event description. Internationalization, accessibility, unit tests, everything. Users of this project would likely fill in some more JS details (by extension--no edits to the generated code of course) and provide CSS for the proper UI layout.

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
