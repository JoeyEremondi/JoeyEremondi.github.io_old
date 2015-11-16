---
title: Elm binaries for Travis-CI
layout: post
---

##UPDATE

Elm is now distrubuted via NPM. You can now easily load it on TravisCI with a `.travis.yml` that looks like this:

    language: node_js
    node_js:
      - "node"
      
    before_install:
      - "npm install elm@VERSION"
      
    script: elm make --yes
    
where you replace `VERSION` with the version of Elm you'd like to test with.

## Elm on TravisCI

If you've ever used cabal on Travis-CI, you know that it's painfully slow, sometimes taking
up to fifteen minutes to compile and install your dependencies for a Haskell package.

The thing is, you shouldn't even need Haskell dependencies if you're just trying to compile Elm programs.
To help speed this up, I've made Elm binaries built on Travis-CI and hosted on my Github Pages site.

To use them, just add `wget http://eremondi.com/elm-travis-cache/NAME_OF_BINARY` to your `.travis.yml` file
to download them, then call them like normal binaries.
You can replace `NAME_OF_BINARY` with any one of `elm`, `elm-get`, `elm-doc`, `elm-repl` or `elm-server`.
There's also `elm_home.tar.gz` which contains the runtime and some other required files.

Alternately, you can download the files here:

* [elm](http://eremondi.com/elm-travis-cache/elm)
* [elm-get](http://eremondi.com/elm-travis-cache/elm-get)
* [elm-doc](http://eremondi.com/elm-travis-cache/elm-doc)
* [elm-repl](http://eremondi.com/elm-travis-cache/elm-repl)
* [elm-server](http://eremondi.com/elm-travis-cache/elm-server)
* [elm_home.tar.gz](http://eremondi.com/elm-travis-cache/elm_home.tar.gz)

Here's an example `.travis.yml` file making use of these:

```yaml
env:
  global:
  - ELM_HOME="./elm-home"
install:
- wget http://JoeyEremondi.github.io/elm-travis-cache/elm
- wget http://JoeyEremondi.github.io/elm-travis-cache/elm-get
- wget http://JoeyEremondi.github.io/elm-travis-cache/elm_home.tar.gz
- chmod +x ./elm
- chmod +x ./elm-get
- tar -xzf elm_home.tar.gz ./
- ./elm-get install johnpmayer/elm-linear-algebra
- ./elm-get install johnpmayer/elm-webgl
before_script:
- cd build
- git checkout gh-pages
- git pull
- cd ..
script: ./elm --make --set-runtime="http://eremondi.com/elm-travis-cache/elm-runtime.js" ObjTest.elm
after_script:
- cd build
#Do stuff with whatever you build
```

If there's ever a problem, or they're out of date, just submit a Pull Request on 
[the README file on GitHub](https://github.com/JoeyEremondi/elm-travis-cache/blob/master/README.md),
and the binaries will be re-generated when I accept it.

Current build status: [![Foo](https://travis-ci.org/JoeyEremondi/elm-travis-cache.svg)](https://travis-ci.org/JoeyEremondi/elm-travis-cache)
