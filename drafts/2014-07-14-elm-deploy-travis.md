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

The `.travis.yml` file is where all of our magic happens.
It's basically a script that gets run every time you push to GitHub,
which we can use to run our tests.

Our `.travis.yml` file starts with a declaration of which branches we want to automatically build.

    branches:
      except:
      - gh-pages
      
By omitting GitHub Pages, we can deploy to Pages from Travis without triggering an infinite build loop.
Next we have our environment variables:
    env:
        global:
        - secure: SomeSecureCode
    - ELM_HOME="./elm-home"
    
This tells TravisCI that we have two environment variables.
One is an encrypted variable, which we'll use later.
The other is the ELM_HOME variable, which we need to set in order
for Elm to work.

Normally, you would build Elm from cabal and install it, but Cabal is very slow
on Travis-CI. Builds can take as long as fifteen minutes.
So, I've got pre-built binaries of the latest Hackage releases of Elm.
The next thing we'll do in our .travis.yml file is download them,
and make them executable.
We also download a copy of a pre-compiled elm-home, which contains the Elm runtime.

    install: 
        - wget http://JoeyEremondi.github.io/elm-travis-cache/elm
        - wget http://JoeyEremondi.github.io/elm-travis-cache/elm-get
        - wget http://JoeyEremondi.github.io/elm-travis-cache/elm-doc
        - wget http://JoeyEremondi.github.io/elm-travis-cache/elm_home.tar.gz
        - chmod +x ./elm
        - chmod +x ./elm-get
        - chmod +x ./elm-doc
        - tar -xzf elm_home.tar.gz ./

At this point, we install any dependencies our project has:

        - yes | elm-get install maxsnew/Error
        
It's important to pipe yes to the install command,
so that the elm-dependencies file gets created if it doesn't already exist.

Now, we have working Elm binaries in our home directory, which we can use to build our project.

    - script:
        - elm --make Main.elm

