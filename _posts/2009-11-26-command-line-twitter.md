---
title:  "Command line Twitter"
permalink: /2009/command-line-twitter
---

Just for the 'geekiness' of it I was looking for a way to use Twitter from the Bash shell prompt.

To read your twitter home timeline:

``` bash
curl -s -u userid:password -g http://api.twitter.com/1/statuses/home_timeline.atom
| xpath -q -e '/feed/entry/title/text()'
| tac
```

Curl is a tool to transfer data from and to a server. The -s option is to keep it silent. -u userid:password is
used to send your twitter account/password (warning: this is HTTP basic authentication so not secure).
-g is used to get the date from the specified url, in this case the data is in atom format (XML). Pipe the output
to the tool xpath (-q to keep it quiet) and select only the title of the entries. Pipe the output to tac
(cat in reverse) to reverse the list of lines so that the latest tweets are at the bottom (as this is more
natural at the command line, no need to scroll up to see the latest updates), one at a line (every title is
prefixed by the sender). The result is extremely readable (at least if the ones you follow know how to express themselves).

And to post a tweet:

``` bash
curl -u userid:password -d status="Hello world." http://twitter.com/statuses/update.xml
```

The web interface of Twitter is much more convenient but at least now I know how to use the tools curl and xpath.
