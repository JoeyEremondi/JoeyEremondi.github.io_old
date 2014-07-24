---
title: Tutorial: Automatically Testing, Deploying and Publishing Elm sites with Travis-CI
author: Joey Eremondi
---

In this post, I'm going to give detailed instructions for how you can use Travis-CI to make your Elm
workflow much easier. There's three main things we'll use Travis-CI for:

* Building and testing your Elm project on GitHub
* Automatically deploying your built site to Github-Pages
* Automatically publishing tagged versions of libraries to Elm-get

# Preliminaries #

First off, I'm assuming you have an existing Elm project pushed to GitHub.
For my examples, I'll use a file Main.elm, which looks like this:

    module Main where
    
    main = plainText "Hello World"
    
    myAdd : Int -> Int -> Int
    myAdd x y = x + y

You can see [here](http://docs.travis-ci.com/user/getting-started/)
for a good guide on how to get Travis-CI set up with your GitHub account,
and enabled for your Elm project.

# Building your project #

Our .travis.yml file starts with a declaration of which branches we want to automatically build.

    branches:
      except:
      - gh-pages
      
By omitting GitHub Pages, we can deploy to Pages from Travis without triggering an infinite build loop.
Next we have our environment variables:
  env:
    global:
    - secure: BeurG3Cuee3lNmRclebXKHAAkPhhXgzNOUpOMl1INLnEtXQ52ZIzyDzeiUA32AIvi3/ICOEOSpiDxI1XWIW9KmJPDjtlImAA6VMe5DLHAwBQYqve/T4NH+jPVyShOoc4RCeevpzThDkzFY08CaamXrnDCqzyItCD/OndJgoZ7Ko=
    - secure: CIK/eqhPHiSVUoOHzm2Rxl7Rg+TozfWD5XkD91uZd1hXN3AlKUae0/3lqRVnyxILPY286TTCuLa84LSvWmirvfximNsRxU4d8TzX3bMM2PlNQYdQoczy+TySiM250d5aq+MP8Lg8joEfsOXJDDvoYEZxvkEzvSJoVWh48QSoDtI=
    - ELM_HOME="./elm-home"

The `.travis.yml` file is where all of our magic happens.
It's basically a script that gets run every time you push to GitHub,
which we can use to run our tests.

Normally, you would build Elm from cabal and install it, but Cabal is very slow
on Travis-CI. Builds can take as long as fifteen minutes.
So, I've got pre-built binaries of the latest Hackage releases of Elm.
The first thing we'll do in our .travis.yml file is download them
