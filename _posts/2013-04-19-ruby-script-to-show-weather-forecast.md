---
title:  "Ruby script to show the weather forecasts in your console"
permalink: /2013/ruby-script-to-show-the-dutch-weather-forecasts-in-your-console
---

Just a quick ruby script to show the Dutch weather forecasts for next week using the
html screen scraping gem [nokogiri](http://nokogiri.org/) and a table formatter
gem called [hirb](https://github.com/cldwalker/hirb).

``` ruby
require 'rubygems'
require 'nokogiri'
require 'open-uri'
require 'hirb'

table = []
headers = []
page = Nokogiri::HTML(open("http://knmi.nl/waarschuwingen_en_verwachtingen/"))
page.css(".realtable")[0].xpath("tr").each do |tr|
  row = []
  tr.xpath("th").each do |th|
    headers << th.text
  end
  tr.xpath("td").each do |th|
    row << th.text
  end
  table << row if row.count > 0
end

puts Hirb::Helpers::AutoTable.render table, :headers=>headers, :description=>false
```

The result in my ZSH (using theme [agnoster](https://gist.github.com/agnoster/3712874))
looks like this:

![screenshot]({{ "/images/2013/knmi.png" | prepend: site.baseurl }})

