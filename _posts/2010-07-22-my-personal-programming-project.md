---
title:  "My personal programming project"
permalink: /2010/my-personal-programming-project
---

## Introduction

Lately I am following the newsfeed [Hacker News](http://news.ycombinator.com) where members can
post links to interesting (IT related) articles. There are a couple of new items per hour and
members can vote these items up or down in the list. Not all articles are interesting for me
and my time is limited so I must choose which articles to read. The site shows 30 items per page,
only the title and the website it's referring to and no description. Based on the title I select
the most interesting items and open them in a new tab in the browser, read them and close the
tabs and finally move on to the next page, etc.

## A cunning plan

My idea is to make an application that can help me by suggesting interesting articles based on
my previous selected items. Over time it should be better in giving suggestions. I finally have
a reason to dive into neural networks. Ten years ago during my traineeship in Bangkok I worked
in the Software and Language Laboratory of [NECTEC](http://nectec.or.th/) and colleagues were
working on Thai OCR and text-to-speech software using neural networks. I got fascinated but
never found a reason to use it in my professional developer career.

I have been developing applications in C# for the last decade so it's time for something new!
The application will be build using Ruby, as this is a language that is on my 'learning'-wishlist
for a couple of years. I am not sure what libraries to use for the user interface yet. At home I
use OSX and at work I use Windows and it would be nice if it runs on both platforms. I still
having trouble getting [wxRuby](http://wxruby.rubyforge.org/) up-and-running at home so I will
have a look at [IronRuby](http://ironruby.net/) in combination with Mono (WinForms) or maybe
another cross-platform GUI toolkit. At least this definitely is a reason to use a pattern like
Model-View-Controller or Model-View-Presenter and make the UI layer as thin as possible.

The user interface will have a list of titles and a button to open a webbrowser with the selected
article. I want to rate every title probably by dragging new items into one of three buckets/lists
(must-read, maybe, not-worth-my-time) or by using 1-3 stars. The title and the score will be used
in the learning part of the application. When the application downloads new items from the newsfeed,
it will give a suggested score to each of them that should be visible to me (eg. colors, size, stars,
sorted). First I will make it work, then I will make it beautiful.

## Conclusion

This is a project with a lot of new things to learn (for me and for the application), but it is
definitely a challenge :-)
