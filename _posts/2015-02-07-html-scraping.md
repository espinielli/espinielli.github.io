---
layout: post
title: HTML Scraping or Surviving Orrible Corporate Tools
date: '2015-02-07T14:59:17'
author: enrico_spinielli
comments: true
tags:
- html
- scrape
- css
- firefox
- hack
modified_time: '2015-02-14T15:26:37'
---

At work I have to fill my weekly timesheet. We use [Planisware][opx]'s Timecard to record time spent on the various projects we are assigned to work on.

So you need to known what the total working time is for the day. For that our badging in and out is recorded and passed to [Chronogestor][cg] which does sums them up and provides both a sexagesimal and decimal format.

When there is an anomaly the sums are blocked till the request you submitted to fix it is implemented...
So I thought I could do some [web scraping][ws] and extract my clockings from the relevant CG's page, make the total for the day (in decimal form: in Timecard we record time in decimal form, i.e. 3.5 is 3 hours and 30 minutes.) and fill Timecard.


## [HTML Scraping][ws] to the rescue ##
Let's take the page for the weekly clockings (in case of anomalies the rows like "Temps pointe" / "Temps valide" are empty):

!["clockings"](http://auto.img.v4.skyrock.net/7206/74747206/pics/3180490115_1_2_jmuRu7c0.png)

I initially tried with [some](http://docs.python-guide.org/en/latest/scenarios/scrape/) [Python examples](https://impythonist.wordpress.com/2015/01/06/ultimate-guide-for-scraping-javascript-rendered-web-pages/) using [lxml][lxml] but got stuck.
CG complaines if you are using Google Chrome (and the tools above use Webkit, the web browser engine, behind the scene).

Also I soon realized CG does a lot of rendering via Javascript while I initially assumed it would have been a simple HTML page with a static table for the clockings. (This would have been to simple to implement! What a mess!)

I decided to go for [SlimerJS](http://slimerjs.org/) the scriptable browser that runs on top of [Gecko](http://en.wikipedia.org/wiki/Gecko_(software)), the web engine of [Mozilla Firefox](mozilla.org/firefox). ([PhantomJS](http://phantomjs.org/) is the equivalent for [Blink/WebKit](http://en.wikipedia.org/wiki/Blink_(layout_engine)) used in [Google Chrome](http://www.google.com/chrome).)

Nothing strange about using Javascript of course but *CG sucks at it*: it is a last millenium tool which has been hastily brought to this century without any knowledge of the web technologies (and any taste for usability and beauty.)
Just check the generated DOM elements and you will see that the [uniqueness (within a page) of `id`](http://www.w3.org/TR/html5/dom.html#the-id-attribute) is not at all respected.

From the page for the weekly clockings shown above, using [Firefox Developer Tools](https://developer.mozilla.org/en/docs/Tools) you can see that the table for the clockings ("Entrées/Sorties" row) can be selected via the following CSS path:

<code data-gist-id="aa410dbe7ff9b05b9c8f" data-gist-file="cg.js" data-gist-line="58" data-gist-hide-footer="false"></code>

### Run it! ###
The scraping is run as follows:

```console
C:\goodies\cg>cg usr:pwd 03/02/2015
```

Where `usr` is the userid and `pwd` is the password for both the internet proxy and ChronoGestor.

`cg` is a simple (stupid?) `.bat` script
<code data-gist-id="aa410dbe7ff9b05b9c8f" data-gist-file="cg.bat" data-gist-hide-footer="true"></code>


### Loggin in ###
The following are the _relevant_ lines from the `cg.js` script (note the _discontinuous_ line numbers)
<code data-gist-id="aa410dbe7ff9b05b9c8f" data-gist-file="cg.js" data-gist-line="6-16,47,54,61-63" data-gist-hide-footer="false"></code>

<br/>
Here we reuse the proxy user and account values which, as per company policy, are the same as our login name and password.
And to do that I had to hack an implementation detail whereby `slimer.js` sets an environment variable `__SLIMER_ARGS` with the options passed on the command line.

<code data-gist-id="aa410dbe7ff9b05b9c8f" data-gist-file="cg.js" data-gist-line="6-16" data-gist-hide-footer="false"></code>


### Extract the clockings ###
In order to extract the right cells for the clocking, I inject JQuery and use `webpage.evaluate()` function from the API in order to execute `extractClockings` (`sel` will be passed as argument to `extractClockings`, it is the CSS path shown above) 

<code data-gist-id="aa410dbe7ff9b05b9c8f" data-gist-file="cg.js" data-gist-line="74-76" data-gist-hide-footer="false"></code>

<br/>
`extractClockings` reads a list of `<a>` elements for each day.

<code data-gist-id="aa410dbe7ff9b05b9c8f" data-gist-file="cg.js" data-gist-line="18-42" data-gist-hide-footer="false"></code>

### Count the minutes ###
The rest is a matter of summing up the minutes and printing the values

<code data-gist-id="aa410dbe7ff9b05b9c8f" data-gist-file="cg.js" data-gist-line="78-114" data-gist-hide-footer="false"></code>


[cg]: http://www.gfi.fr/gestion-des-temps/gestion-des-temps.php "Chronogestor"
[opx]: http://en.wikipedia.org/wiki/Planisware "Planisware"
[ws]: http://en.wikipedia.org/wiki/Web_scraping "Web Scraping"
[lxml]: http://lxml.de/ "lxml"