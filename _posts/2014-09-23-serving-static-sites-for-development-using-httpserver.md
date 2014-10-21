---
layout: post
title: Serving Static Sites for Development Using 'http-server'
---

# Serving Static Sites for Development Using 'http-server'

<p class="meta">23 September 2014 - San Francisco</p>

Ever come across an error like this while developing a static site on your local machine?

> XMLHttpRequest cannot load file://path/to/file. Received an invalid response. Origin ‘null’ is therefore not allowed access. 

This error is due to the CORS policy implemented by many browers (I got this message using Chrome Dev Tools), which is there for your protection. You may see this if you are trying to place an AJAX request to a local file, say loading data from a .txt or .csv file instead of messily including this in a local array or div in your JavaScript / HTML respectively.

The error will go away once you deploy your site to a real server and files are served over HTTP, and thankfully you can replicate that behavior on your local machine! Enter the npm library ‘http-server’:

First install the package globally on your machine

<pre><code>$ npm install -g http-server
</code></pre>

Then navigate to your project folder that includes index.html at the top level

<pre><code>$ cd /path/to/myApp
</code></pre>

Then start the server

<pre><code>myApp $ http-server
</code></pre>

View in your browser

<pre><code>http://localhost:8080
</code></pre>

And you’re done!