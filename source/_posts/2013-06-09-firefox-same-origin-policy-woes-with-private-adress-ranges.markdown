---
layout: post
title: "Firefox: same origin policy woes with private adress ranges"
date: 2013-06-09 13:48
comments: true
categories: 
---

Suppose you have an html page and you want to embed an [SVG](http://css-tricks.com/using-svg/) in it for later manipulation. One of the possible ways to do it is using an `object` tag like this:

``` html
<div id="the_logo">
  <object type="image/svg+xml" data="images/awesome_logo.svg" id="awesome_logo">
    You're missing out on an Awesome logo with that old browser!
  </object>
</div>
```

And using some javascript to manipulate, say, a node's opacity:
``` javascript
function fade_out_lettering(){
  // Get the svg content document embedded in out document 
  var svg = document.getElementById('awesome_logo').contentDocument;

  // use jQuery to select an SVG element, using svg as the search context
  $('#lettering',svg).animate({opacity: 0});
}
```

Now, suppose that you use private addresses in the context of your development environment. You access your development (virtual) machine using some ip address in Chrome:

`http://10.0.0.10:3000/`

And you see your glorious svg logo lose it's letters in a nice fade. Check with Safari, and the same beautiful experience. Check with firefox, aaaaaand... nothing!
Open up the inspector and you'll see something like this (or similar):

![Firefox](/images/firefox_inspector.png)

What the hell? Permission denied in the context of access to embedded documents (iframes, svgs...) usually means violations of the [same origin policy](http://en.wikipedia.org/wiki/Same_origin_policy), but in this case the contents are all local, and share the same protocol, host and port number. Besides, it works on all other cool browsers! What's going on?

Well, it seems firefox has some issues checking same origin policy with private adresses (or is it any ip address?). Just to validate this theory, use an ip-to-name tool like [xip.io](http://xip.io) to check the page again. You access your development maching using a name instead of an ip address:

`10.0.0.10.xip.io:3000/`

And _voilá_! The letters also fade in firefox (version 21 in my case)! So now you can deploy your shiny logo confident that it will work when your users view it served from production machines. I'm assuming you do use a domain name for your site...

This firefox idiosyncrasy applies to embedded svgs, but also to iframes, and probably to any other object you can embed in your page.

Hat tip to [@luismreis](http://twitter.com/luismreis) for the xip.io trick. Thanks!
