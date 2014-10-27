---
layout: post
title: Sane GitHub Hooks with Plain Node.js and Bash
---

# Sane GitHub Hooks with Plain Node.js and Bash

<p class="meta">20 October 2014 - Detroit</p>

This morning, I decided to try out another automated deployment strategy (see [previous post on using Travis-CI](http://johnheroy.com/2014/10/17/continuous-firebase-deployment-with-travis.html)) using GitHub's webhook service. NOTE: I used a VPS (Ubuntu 12.04 on Microsoft Azure) for this setup, so there was no "out of the box" hook available or git deployment (that old "git push heroku master" jam) on this hosting side.

You may be familiar with this workflow:

<pre><code>git add .
git commit -m "Some commit message"
git push origin master
...
ssh user@somevps.io
cd /path/to/myApp
git pull
forever restartall # let's say I'm running Node.js for the main project
</code></pre>

It's not the worst workflow ever, but it's totally doable to eliminate the whole ssh step, which can surprisingly eat a lot of time and take away from writing code. I will present one simple dependency-free (assuming you are on Node.js and some Linux server environment) solution to streamlining your deployment strategy.

I first fell upon [this blog post](http://codesquire.com/post/ContinuousDeployment), which got the idea in my head that I could simply listen on another port in my main Node.js server file. Ordinarily I wouldn't assume that a module doesn't work and trust the author, but after seeing [issue #20](https://github.com/danheberden/gith/issues/20) on GitHub and trying the suggested fix without success, I decided to just code up the solution myself since it was only a few lines of code and way more fun!

Basically, I

1. created a separate server instance using Node's 'http' included module to listen on a port other than 80 (and simulataneously opened this port on my VPS in the Azure management console--don't forget this step!), then
1. In my 'requestListener' function passed to http.createServer, listen for the request 'data' event, save this as a string, then parse to a JavaScript object and validate the JSON request (i.e. repo name, branch, etc. so you aren't deploying for a 'develop' branch), then finally
1. execute your bash script using the 'execFile' function in Node's 'child_process' module

It's that simple! Code below for reference:

# <code>server.js</code>

<pre><code>// GitHub hook
var execFile = require('child_process').execFile;
var githPort = process.env.GITHOOK_PORT || 4000;

var http = require('http');
http.createServer(function(req, res){
  console.log('new webhook received');
  var body;
  req.on('data', function(chunk) {
    body = (body) ? body + chunk : chunk;
  });
  req.on('end', function(){
    body = JSON.parse(body.toString());
    if (body.repository.name === 'my-repo' && body.ref === 'refs/heads/master'){
      console.log('executing hook.sh');
      execFile(path.join(__dirname, './hook.sh'), function(error, stdout, stderr){
        if (error) console.log('there was an error running the hook.sh', error);
      });
    }
  });
  res.writeHead(200);
  res.end();
}).listen(githPort, function(){
  console.log('githook listening on port', githPort);
});
</code></pre>

# <code>hook.sh</code>

<pre><code>#!/bin/bash

echo "Deploying master branch at $(date)" >> ~/deployment_log.txt

git pull

forever restartall
</code></pre>
