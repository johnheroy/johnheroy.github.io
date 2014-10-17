---
layout: post
title: Continuous Firebase Deployment with Travis-CI
---

# Continuous Firebase Deployment with Travis-CI

<p class="meta">17 October 2014 - San Francisco</p>

We just started our first group projects at [Hack Reactor](http://hackreactor.com) and we decided to go with [Firebase](http://firebase.com) as a backend service for easy-to-use sockets / events and also using Firebase hosting to serve our static front-end assets.

As the "scrum master" on my team, I wanted to design a work flow that would make it as easy as possible to deploy down the line. At the time of writing, deployment to firebase is as easy as running 'firebase init' to create a firebase.json file in your directory that specifies the public folder, then running 'firebase deploy' to actually push the public folder to the server.

This is honestly a fairly simple deployment scenario compared to some gnarly alternatives involving custom VPS setups, but as a group I did not want any one person to be solely responsible for pushing to the server nor have any accidental local pushes without incorporating upstream changes / passing tests. Therefore, enter [Travis-CI](http://travis-ci.org).

Travis is a "continuous integration" service which is free for open-source repos. It basically listens for new pull requests / commits to your git repository (GitHub in our example), and then immediately clones the repo into a temporary Linux environment and runs tests ('npm test' for Node.js projects) as well as other user-supplied scripts in your repo's '.travis.yml' file.

The initial naive solution we came up with to deploy immediately to firebase was to supply this set of steps at the end of our .travis.yml:

<pre><code>after_success:
  - npm install -g firebase-tools
  - firebase deploy
</code></pre>

But then travis would hang after firebase prompted the terminal with the line "Please enter your email addres to login" etc. So the next iteration we came up with was to 1) create travis environment variables with our firebase credentials and 2) echo an interpolated string into the 'firebase deploy' command to automatically enter these prompts. Skipping a few intermediate steps trying to get the bash syntax correct, we came up with the following:

<pre><code>after_success:
  - mkdir /home/travis/tmp
  - echo -e "${FIREBASE_EMAIL}\n${FIREBASE_PASSWORD}" | firebase deploy
</code></pre>

Note that we decided to create a tmp folder in the travis environment which was a solution found in this [stackoverflow answer](http://stackoverflow.com/questions/25668744/cant-deploy-to-firebase-get-enoent-error/25682675#25682675). But then we started realizing that Travis was now deploying to firebase on both pull requests and commits to the master. We still wanted to see the result of the test suite with each pull request to make sure we hadn't screwed up our code, but at the same time we also want to 'gate' each push to firebase with the test suite. So we played around more with bash syntax and came up with a conditional solution to arrive at the following block in our .travis.yml:

<pre><code>after_success:
  - mkdir /home/travis/tmp
  - sudo $(which npm) install -g firebase-tools
  - if [[ "${TRAVIS_PULL_REQUEST}" == "false" ]]; then echo -e "${FIREBASE_EMAIL}\n${FIREBASE_PASSWORD}" | firebase deploy; fi
</code></pre>

Hooray! Continuous integration with Firebase using Travis-CI. Check us out and support our project OurGlass on [GitHub](https://github.com/unexpected-lion/ourglass)