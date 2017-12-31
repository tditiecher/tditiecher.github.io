---
title:  "Moving from Jekyll to Pretzel"
permalink: /2016/moving-from-jekyll-to-pretzel
---

I created the previous version of this website with the static website generator
[Jekyll](https://jekyllrb.com/) on my macbook. Now I switched back to a Windows
machine and as Jekyll is not officialy supported on Windows I decided to look for
an alternative written in C# (just for the fun of it).

I found a project named [Pretzel](https://github.com/Code52/pretzel) that follows
the same conventions as Jekyll so that I wouldn't have to completely recreate my
website. As Pretzel is open source and was missing the feature to specify defaults
(like layout) in the global configuration file I spent an evening forking the Github
repository and adding this feature. I'm glad that the owner accepted my pull request
so that I and other Pretzel users don't have to specify the layout in the frontmatter
of each post. 

With some minor tweaks in my template and the markdown of my posts I was
able to generate my website again almost identical as before. The big difference 
was that Jekyll uses [Sass](http://sass-lang.com/) and Pretzel uses
[Less](http://lesscss.org/). To save some time I just copied the old generated css
and didn't bother to convert the original Sass to a Less version.

