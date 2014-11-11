---
layout: post
title: Code Coverage Statistics Using Coveralls and Istanbul
---

# Code Coverage Statistics Using Coveralls and Istanbul

<p class="meta">20 October 2014 - San Francisco</p>

I recently wrote a small utility npm library for one of my personal projects and decided to use it as an example to explore best practices for well-tested and documented code that other people may be able to use in their own projects. That module is called [prettify-pinyin](https://www.npmjs.org/package/prettify-pinyin) and in short will turn 'ni3 hao3' into 'nǐ hǎo'. I was importing a Chinese-English library from plaintext to a sqlite database and needed a utility to display Mandarin pronunciation notes to the user with convenient tone marks as commonly seen in other learning materials.

My general approach was based on [this slide deck](http://slidedeck.io/saintedlama/be-a-good-npm-module) entitled "Be a Good NPM Module". I already had the Travis-CI integration set up so I'm just going to focus on what I did with Istanbul and Coveralls. [Istanbul](http://gotwarlost.github.io/istanbul/) is essentially a JavaScript code coverage library that will check statement, branch, and function coverage statistics by inserting lines of code into your JavaScript as your tests run (in my case mocha) and then report back how many of its inserted lines were called. Statement and function coverage should be fairly intuitive but to clarify branch coverage tests all the possible outcomes of control flow in your application. So if you have some `if else` statement and only ever run code when the first conditional evaluates to `true`, that will seriously reduce your branch coverage statistic.

First off, sign up for [Coveralls](https://coveralls.io/) with your GitHub account and enable the repos for which you want code coverage statistics (note: for open source projects these should be set up in Travis already, and then once you configure your repo properly, Coveralls will automatically grab code coverage stats for the next build). Then you want to upload your `package.json` to have a `"scripts"` key like the below:

<pre><code>"scripts": {
  "test": "mocha",
  "cover": "istanbul cover ./node_modules/mocha/bin/_mocha --report html -- -R spec"
}
</code></pre>

Then you want to update your `.travis.yml` to install `istanbul`, `coveralls`, and `mocha-lcov-reporter` like so:

<pre><code>language: node_js

node_js:
  - "0.11"
  - "0.10"

branches:
  only:
    - master

before_script:
  - npm install -g istanbul
  - npm install coveralls
  - npm install mocha-lcov-reporter

after_script:
  - NODE_ENV=test istanbul cover
    ./node_modules/mocha/bin/_mocha --report lcovonly -- -R spec &&
    cat ./coverage/lcov.info |
    ./node_modules/coveralls/bin/coveralls.js && rm -rf ./coverage
</code></pre>

Essentially what is happening in the `after_script:` block is istanbul runs an `lcovonly` code coverage report, saves that info to `coverage/lcov.info`, pipes that data to the coveralls library, and then deletes that `coverage` folder for cleanup at the end. There is a lot more power to Istanbul when you run locally and see all these other statistics (i.e. branch coverage), so beware that this `lcovonly` statistic is only one small part of your code coverage picture (although you can nicely advertise it on your README with a single number).

Finally, add a shiny new code coverage badge from Coveralls, commit and push up your new changes, and then bask in the glory of your bulletproof code! Generally speaking I have heard you should shoot for 85%+ code coverage but your mileage will vary.
