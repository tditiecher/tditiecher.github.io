---
title:  "The birth of Lapsang"
permalink: /2010/the-birth-of-lapsang
---

In my previous post I described a personal programming project I was planning.
I wanted to use Ruby as the programming language of choice, but there are a couple
of reasons for going back to C#: first, I couldn't find a working GUI toolkit for
Ruby (at least working on my OSX machine). I spent a full day and decided to move
back to System.Windows.Forms (I can use the Mono implementation). Another reason is,
as [Michel](http://www.justyouraveragegeek.com/) already commented, a new language
will slow me down too much and at this moment I prefer a working application above
a new language.

The hardest part of a new project is the name and after a deep thought I came up
with Lapsang ([a black Chinese tea](http://en.wikipedia.org/wiki/Lapsang_souchong)).

When downloading the data from the RSS-feed from HackerNews I saw there are only
25 items in the document. I need more items to train the network so I decided to
make a quick-and-dirty screen scraper to browse the website and download the items
of multiple pages. This will probably not be part of the final product, I prefer a
pure RSS feed (or multiple, maybe via an OPML file) as my datasource.
