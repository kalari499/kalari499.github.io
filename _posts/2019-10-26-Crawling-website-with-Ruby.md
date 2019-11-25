---
layout: post
title: Tips&#58; Crawling websites with Ruby + Nokogiri
description: A quick tutorial on web scrapping in Ruby with the help of Nokogiri gem.
category: Tips
---

### Context

In this post, we are going to take a look on how to crawl content from the Internet, with the help from Ruby and Nokogiri gem.

As an example for demonstration, we are going to crawl the data from [list of urban areas in Nordic countries from Wikipedia.](https://en.wikipedia.org/wiki/List_of_urban_areas_in_the_Nordic_countries)
To be more precise, we are going to get the name of cities, along with their ranking, country and population and export them into CSV format.

Prequisite for the tutorial is just basic HTML/CSS and understanding of CSS selectors.

![Screenshot](/assets/02-1.png)

### What is web crawling, and what can I do with it ?

The idea behind web crawling is simple.
There are certain data and information available online that we are interested in.
However there aren't any available datasets publicly for downloading.

Web crawling is the action of extracting that critical and interesting information.
Technically speaking, we first download the whole HTML document, then using CSS selector to pick out particular element that we want (hence HTML/CSS prequisite).
A small note that regular expresion can always be used here for better filtering, for example extracting email addresses.

Web crawling allows us to do many things.
For example, we can create our own custom dataset for statistic analysis or for machine learning.
In general you can not rely on other people to create an API or a XML dataset for you right ?

### Why Ruby ? What is Nokogiri ?

Ruby is a general purpose language, suitable for scripting.
I like Ruby so I write this in Ruby, but you can use Python (requests and BeautifulSoup) for this task as well.

[Nokogiri](https://github.com/sparklemotion/nokogiri) is a HTML, XML, SAX, Reader parser written in Ruby.
It is a big library with a lot of features.
We are going to use its CSS seclector feature to extract elements

Instal Nokogiri (requires Ruby 2.3.0+) with
```bash
gem install nokogiri
```

### Bread and Butter

The main logic of the code is very simple, thanks to Nokogiri API.
```ruby
html = Nokogiri::HTML(open(url))                # Download the HTML
rows = html.css('table')[0].css('tr')[1..-1]    # Get all the rows

rows.each do |row|                              # Loop through each row
  row = row.css('td')
  city = row[1].css('a')[1].text                # Get city name
  country = row[1].css('a')[0]['title']         # Get country name
  population = row[2].text.gsub(/\[.*\]/, '').gsub(/\,/, '').to_i   # Get population
end
```

First you download the HTML source code and pass it to Nokogiri for parsing.

Now let's use our favourite h4x0r Inspect Element tool (F12 in most common browsers),
we can see that the big table we are interested in is inside the first `<table>` tag.
So we call `#css('table')` gives us the list of elements inside `<table>` tag, then `#[0]` for the first element, a.k.a that table.

Simple right ?

Same method apply, calling `#css('tr')` on that table gives us all the rows, then slice `#[-1..1]` to omit the first row, which is the header we don't need.
To get the text of an element, we call `#text` (see example `city`).
To get a class of an element, we call `#[]` (see example `country`).

See Nokogiri documentation for more details and more API.

Tips for choosing the right element we want, pay attention to the inspect tool for the correct tag/class.
Here for each row, we can extract the name of city by getting the text of the second link in `<a>` tag for each.

![Screenshot](/assets/02-2.png)

A small note here, getting the text of an element in Wikipedia might includes some footnote references.
For example in `population`.
Here use basic regex to get rid of footnotes in square brackets `[]`.
We also get rid of the commas inbetween numbers for cleaner data.

### Full crawling script

```ruby
#!/usr/bin/ruby

require 'nokogiri'  # Import Nokogiri gem
require 'open-uri'  # Import OpenURI gem
require 'csv'       # Import CSV gem

url = 'https://en.wikipedia.org/wiki/List_of_urban_areas_in_the_Nordic_countries'
html = Nokogiri::HTML(open(url))                # Parse the HTML document
rows = html.css('table')[0].css('tr')[1..-1]    # Get all the rows

CSV.open('out.csv', 'wb') do |csv|              # Open CSV file for writing
  rows.each do |row|                            # Loop through each row
    row = row.css('td')
    rank = row[0].text                          # Rankg
    city = row[1].css('a')[1].text              # City name
    country = row[1].css('a')[0]['title']       # Country name
    population = row[2].text.gsub(/\[.*\]/, '').gsub(/\,/, '').to_i   # Population
    csv << [rank, city, country, population]    # Write to CSV file
  end
end
```

### The End

With just a few lines of code, we have managed to write a very simple crawler.
You can then pass it for visualization and many other things.

Hope you learn something news from this.
Try it yourself at home.
Make many more datasets.

See you next time.
