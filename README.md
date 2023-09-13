# Warframe Damage Calculator

## About

This is a personal side project, sponsored by the *silly foundation<sup>TM</sup>*. I cannot guarantee its completion.

Unfortunately wails doesn't currenly support cross compile so anyone on mac and linux will have to compile themeselves (and windows too until a working version is realesed)

## Compile

To compile or develope locally:

1. Install the [Wails CLI](https://wails.io). Note that you'll also need Go and Node.js.
2. Clone this repo
3. Run ```npm install``` *(this should be done in the ```/frontend``` directory)*
4. Run ```wails dev``` for a dev version or ```wails build``` to build a platfor specific binary

## Features

- Availible:
    1. look up warframe weapons and display their stats
    2. search works with non exact names
    3. beginnings of a damage calculator
- Planned:
    1. finished damage calculator
    2. a system for displying conditional modifiers
    3. some kind of icon or image display

## Notes

I am avoiding the use of the go bindings that wails provides as I want to make this hostable as a website in the future, but I am much more comfortable in go than in js so I might go back to that if i get hardsruck on something


