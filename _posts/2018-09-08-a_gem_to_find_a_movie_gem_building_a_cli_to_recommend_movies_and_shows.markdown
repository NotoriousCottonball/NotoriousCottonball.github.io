---
layout: post
title:      "A Gem to Find a (Movie)Gem:  A CLI App for Streaming Recommendations"
date:       2018-09-08 04:39:03 -0400
permalink:  a_gem_to_find_a_movie_gem_building_a_cli_to_recommend_movies_and_shows
---


My first Flatiron School project involved creating a Ruby Gem with a Command Line Interfact(CLI) and user input requested access to data from a web page. The project required a strong grasp of Ruby Gem Code Hierarchies, Ruby's `$LOAD_PATH`, the parsing capabilities of the Nokogiri Gem, and Ruby Object Relationships. The application had to exhibit strong Object Oriented Programming design patterns and efficiently extract and display data that is kept up-to-date at an external source.

---------

**what_to_watch** is on [RubyGems](https://rubygems.org/gems/what_to_watch).

To install the gem, type `gem install what_to_watch` on the command prompt

To run the application, type `what_to_watch`

The **what_to_watch** code repository is on [github](https://github.com/NotoriousCottonball/what_to_watch).

---------

## I. The `what_to_watch` Ruby Gem


`what_to_watch` is a Ruby Gem that returns best-reviewed and recently_added movies/television based on a user's access to popular streaming services. The application asks the user to respond Yes or No (y/n) to whether they have access to Netflix, Amazon Prime, HBO, Hulu, or Showtime. It then asks if they'd like to see recommendations for Best Movies, Best TV Shows, or Recently-Added Movies/TV. 

The application then scrapes the Vulture.com/streaming page according to user input. It returns a filtered list of shows, designating those available and unavailable to the user, and indicating the streaming service at which the user could watch them. 

The user may select an item from the returned list by number, upon which the application will search IMDB.com for the item and display details including a description, the genre, the year, the director/creator/writers/stars, and IMDB item page url. 

The user may then select a different item from the original returned list, return to the main menu to see a different set of recommendations, or exit the program. CLI controller allows the user to exit at any time by simply typing 'exit' or 'Exit.'

Vulture.com's Streaming Hub provides an excellent source of recommendations based on very thorough and in-depth ratings/reviews. IMDB.com provides a repository of movie/television information that is searchable and easy to scrape. 

The first is somewhat hard to scrape and get details from, but provides an up-to-date source of movie/television streaming recommendations that are the result of a rigorous review and ratings process. The latter easily provides key details/additional information. 

The idea of `what_to_watch` is to sort (by streaming_service) and filter (by best-reviewed-movies, best-reviewed-television, or recently-added-movies/tv) the results of Vulture.com's rating/review system and use IMDB.com to fill in the relevant details. 

The application uses the `:title` attribute of the user's selection from the returned list of items recommended on Vulture.com/streaming to generate a URL Parameter Search on IMDB.com/find. It then scrapes the search results page for the URL Extension for that title's IMDB page, and then scrapes that page for the requested information.

A couple days after I finished a working version of  `what_to_watch`, Vulture.com/streaming reformatted their homepage layout. Unfortunately, my application no longer worked since my Scraper method relied on the previous HTML and a completely different Document Object Model structure.  With live pages, a slight change to the nesting or the renaming of a class can break a scraping script.

However, rewriting the Scraper method for Vulture.com/streaming actually enhanced the application since it now draws on much more structured HTML with patterns that will likely remain consistent update after update. I tried to make the most of the new architecture, refactoring code in the CLI and Class Models to ensure the ease of slight readjustments in the future. 


## II. The Code


### A. Dependencies


A Ruby Gem is basically a plugin. But rather than including third party code directly in a project, you just reference the name and version. So Ruby Gems are an automated way of managing code hierarchies. The code for required Ruby Gems is still downloaded, but it's kept separate from the application. 

Enter the Gemfile. To help use the code inside of Gems, RubyGems(a standard package manager for the Ruby Language) overrides the `require` method. In the case of requiring `pry` for example, an error message is raised by `require "pry"`, and RubyGems looks in the Gemfile for the file dependency. Specifically, it looks inside Gem metadata for the file `pry.rb`  It activates the Gem containing that file and adds the code inside that Gem to Ruby's $LOAD_PATH. 

Bundler helps RubyGems find all the Gems in the Gemfile and all their dependencies. It also acts like a package manager, determining the full set of dependencies required by the project and the subdependencies needed by those dependencies, and so forth.

So, at the top of the first file the application loads, you indicate: `require "bundler/setup"`.  As of Ruby 1.9, you no longer need to indicate `require "rubygems"`. It's automatic. 

Bundler configures the load path, clearing it and then filling it with the Gems and Files required in the Gemfile. RubyGems can then easily activate those Gems, adding their `lib` directories to the $LOAD_PATH. Now `require 'pry'` makes sense. The required file is the main, titular `lib` directory file `pry.rb`.  With the `lib` directories of Gems added to the $LOAD_PATH, we can `require` files by name directly from them. So, Bundler kind of configures the $LOAD_PATH and manages it, using RubyGems, `require` and the `$LOAD_PATH`  array. `bundle install`  then finds versions of dependencies that work together and makes sure to install the exact same versions on every machine that runs the application. Through the use of heuristics, shortcuts, and tricks, Bundler resolves dependencies before runtime and provides a consistent application experience. 

When building a Ruby Gem, you specify all these dependencies, along with metadata declarations, in the `.gemspec`. You just have to let know Bundler what to expect:

```
source "http://www.rubygems.org"

gemspec
```

That's it. Bundler will add the the load paths listed in the `.gemspec`. The `.gemspec` adds the Ruby Gem's own `lib` directory to the $LOAD_PATH with:

```
lib = File.expand_path("../lib", __FILE__)
$LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)
```

It then uses RubyGems to load the `lib` directory of every Gem specificied. 

The resulting dependency structure allows me to simply write the following in my executable `what_to_watch`:

```
#!/usr/bin/env ruby

require "bundler/setup"
require "what_to_watch"

WhatToWatch::CLI.new.start
```

Again, having required bundler/setup means I can require a file in the `lib` directory directly with `require "what_to_watch"`.

`what_to_watch.rb` then acts as an environment, declaring all the application's file dependencies:

```
require "open-uri"
require "nokogiri"
require "cgi"
require "pry"


# File Dependencies.
#Load version.rb first 
#to define Module WhatToWatch.

require "what_to_watch/version"
require "what_to_watch/cli"
require "what_to_watch/scraper"
require "what_to_watch/show"
require "what_to_watch/best_movies"
require "what_to_watch/best_tv"
require "what_to_watch/recently_added"
```

By requiring `what_to_watch/version.rb`  through `what_to_watch.rb`,  the application defines the module `WhatToWatch` before executing `WhatToWatch::CLI.new.start`. 

The $LOAD_PATH is configured and managed from the start of the first file loaded. Bundler adds the `lib` directory of `what_to_watch` and the `lib` directories of every required Gem. Right after `require bundler/setup`, files can be required realtive to the $LOAD_PATH where their directories are. 

That's why it's so important to namespace a Gem's files carefully. If my Gem `what_to_watch` had a `pry.rb` file directly in the `lib` folder, that'd be a problem. Both my `require pry` and Pry's `require pry`  It's standard practice to name the main `lib` file after the Gem and to organize all the code in a unique namespace (module). Most of the popular Ruby Gem Projects like `rake` structure their code like this: matching name for the Gem, Module, and the main `lib` file, which they use to require dependencies and set defaults. 


### B. The CLI Controller


The application is controlled through the WhatToWatch::CLI class, initiated either directly through it's `#start` instance method, or via the executable `bin` file `what_to_watch`. The basic flow is:
 
1. Greet User
2. Ask User Which Streaming Services They Have
3. Main Menu: Ask User Which Category of Recommendations They Want
4. Scrape That Category of Recommendations and Then Sort, Filter, and Display the Results based on the Previous Streaming Services Responses
5. Options Menu: Ask The User if They'd Like more Details/Information on a Particular Item in the List
6. Search IMDB.com for the Title of the Selected Item and Scrape the Search Result Page for the Item Page URL
7. Scrape the Item Page for Additional Details and Display them
8. Item Menu: Ask The User If They would like to Go Back to the Main Menu, Request Details for a Different Item,
or Exit **

The Controller encapsulates the code for the various menus in separate CLI Dialogue methods apart from the `#start` menu. The CLI class has an `@input` and `@streaming_services` attribute, the latter in the form of a hash that is edited based on responses at the start of the application. 

The main challenge with the CLI Controller was allowing the user to Exit the program by typing `Exit/exit` at any point while looping if the user entered invalid input . The solution was to nest the conditionals following each call for user input in a while loop. 

An `#exit?`method watches for  `@input.downcase == "exit` and the `#start` method consists of nested loops determined by ` while !exit?`. This serves to both repeat the loop in the case of invalid input (which calls again for input) and to ensure that once the user inputs `Exit/exit`, subsequent loops are skipped over right down to the `#exit` method call. 

At the deepest level of the application, the Item Menu loops in the case of invalid input, breaks out to the outer loop in case the user wants to select another Item, or throws back to the catch at the start of the Main Menu Loop.

A section of the `#start` method illustrating the Nested Logic is below:

```
def start
    which_streaming_services
    while !exit?
      catch(:main_menu) do
      main_commands
      while !exit?
      case @input
      when "1"
        WhatToWatch::BestMovies.add_shows
        WhatToWatch::BestMovies.list(@streaming_services)
        options
        while !exit?
        if valid_number?(WhatToWatch::BestMovies.all)
          WhatToWatch::BestMovies.print_item(@input)
          item_options
          while !exit?
          if @input == "y"
            options
            break
          elsif @input == "n"
            throw(:main_menu)
          else 
            invalid_command
          end
          end
        else
           invalid_command
        end
        end
				[...]
```

`#which_streaming_services` cycles through five requests for input, using conditional returns and breaks to allow valid input to proceed and a single input of `Exit/exit` to exit.

```
def which_streaming_services
    puts ""
    puts "Which Streaming Services do You Have Access To?"
    puts ""
    puts "Please Enter y or n to Answer each Question:"
    puts "(Exit: Type exit)"
    puts ""
    puts "1. Do You have Netflix?"
    @input = gets.strip.downcase
    while !exit?
    if @input == "y"
      @streaming_services[:netflix] = "y"
      break
    elsif @input == "n"
      @streaming_services[:netflix] = "n"
      break
    else 
       invalid_command
    end
    end
    
    return if exit?
    puts "2. Do You have Amazon Prime?"
    @input = gets.strip.downcase
    while !exit?
    if @input == "y"
      @streaming_services[:amazon_prime] = "y"
      break
    elsif @input == "n"
      @streaming_services[:amazon_prime] = "n"
      break
    else 
       invalid_command
    end
    end
    
    return if exit?
    puts "3. Do You have HBO?"
		[...]
```


### C. The Class Models


The `BestMovies`, `BestTV`, and `RecentlyAdded` classes share identical functionality except for their storage array `@@all`, the section number they pass to the Scraper, and the heading displayed when they list recommendations. 

Therefore, they all inherit from a `Show` class which details the attributes and methods shared by all three class models. 


### D. The Scraper Service Object and URL Parameter Search

**1.) Scraping Vulture.com/streaming**

The `Scraper` class has two main methods.  The first parses the HTML of Vulture.com/streaming and passes the title and streaming service of recommendations in the designated category as arguments to initialize instances of one of the three models. The model stores the objects generated in the `@@all` array and later calls on the `Scraper` class to set the attributes of a selected instance after parsing the HTML of the item page on IMDB.com. 

The `Scraper` class relies on the `nokogiri` gem, which enables parsing HTML input by creating a Data Object with a host of useful properties and methods for efficient extraction of information. Using CSS selectors on a Nokogiri Data Object returns a NodeSet, which acts very much like an array. Essentially, `Nokogiri::HTML(open("URL") ` allows for parsing and organizing the data of a webpage in a way that fully leverages the power of the Ruby Enumerable.

Initially, scraping Vulture.com/streaming was incredibly difficult due to a less structured design that didn't allow for full use of Ruby's Enumberable methods. The redesign, while forcing me to write a new scraper method, took well to nested iterators. 

The Vulture.com/streaming homepage consists of four major blocks of recommendatations. They are always in the same order: "Recently Added," "Expiring," "Best TV," and "Best Movies." The application omits the second category and uses three basic categories in the interests of simplicity. The model object (`BestMovies`, `BestTV`, or `RecentlyAdded`)  calling on the `Scraper` provides the position of the corresponding block.


`#self.scrape_vulture(model)` accepts one of the model objects as an argument, produces `doc`, a Nokogiri Data Object, selects an array-like collection of divisions with attribute `data-editable` equal to `"main"`, and  sets the `block` equal to the one corresponding to the model that called on it.

   ```
doc = Nokogiri::HTML(open("http://vulture.com/streaming"))
   block = doc.css("div[data-editable='main']")[model.section]
```

Each `block` is composed of rows and the items in those rows. The first item of each row contains the streaming service, and the rest contain the names of the recommended movies and tv  available on that streaming service.  The structure of the HTML lends itself to nested iteration through the `#each` method:

```
block.css("div.column-item").each do |row|
      row.css("section").drop(1).each do |item|
```

Using CSS selectors, the scraping method reaches into each item of each row, skipping the first via the `drop` method, to operate only for every movie/tv item. It then calls on the passed Model Object to initialize a new instance for each of those movie/tv items:

```
model.new(
          item.css("div[itemprop='caption']").text.split("\n")[1].strip.gsub("  ", " "),
          row.css("section")[0].css("h3").text.strip.capitalize
					)
```

The Object Instance initializes with a `:title` and `:streaming_service` attribute, the first extracted from the movie/tv items and the latter extracted from the streaming service item skipped over in the nested iteration. Editing and shaping the results into visually appealing form makes good use of Ruby's String methods.

**2.) Searching and Scraping IMDB.com**

The second scraping method extracts the rest of the Object Instance's attributes when the user requests more information on a movie/tv show. It's a two step process since the scraping method must first determine the URL for the IMDB.com page for that particular movie/tv show. 

`#self.scrape_imdb(object)` accepts the object instance as an argument and calls on `#self.get_item_page(object)` to obtain the URL. `#self.get_item_page(object)` performs a URL Parameter based search to obtain a search results page. It then scrapes the search results page to obtain the URL Path for the item page and extracts the requested information. 

#self.get_item_page(object) utilizes the `cgi` gem's `#escape` method to URL Encode the string attribute given by `object.title` for use as a Query Parameter. The Query Selectors used are fairly easy to obtain.  Cycling through the search options for a given search input on IMDB.com/find indicates the use of standard key-value pairs in the query component following the first `?`. It's obvious that `q=` denotes the query, and only a little less obvious that `s=tt` denotes searching titles, `s=nm` searching names, `s=kw` searching keywords, and so forth. 

The search results page for a particular title is therefore given by concatenating the URL Encoded title with "https://www.imdb.com/find?s=tt&q=". For some reason including the word "Season"(but not the number) in a title search throws off the search, so it's removed before encoding and concatenating. The scraped search results page is given by:

```
search_results_page = Nokogiri::HTML(open("https://www.imdb.com/find?s=tt&q=" + 
    CGI::escape(object.title.gsub("Season ", ""))))
```

The Document Object Model for the search results page exhibits  a table list format, with the first search result's `<a>` element's href attribute set to the URL Path of that title's IMDB Page:

```
url = "https://www.imdb.com" + 
    "#{search_results_page.css("td a").attribute("href").value}"
```

Scraping the IMDB page is relatively straightforward, with some shaping and editing in preparation for visual display. Given the various combinations of types of cast members (Director, Writer, Creator, Stars, etc.) extracting the `cast` attribute in the form of a hash required a bit of iteration:

```
object.cast = {} 
    item_page.css("div.credit_summary_item").each do |category|
      object.cast[category.css("h4").text.strip] = category.css("a").collect{|tag|tag.text.strip}.
      join(", ").gsub(", See full cast & crew", "")
```


## III. The Takeaway 


The CLI Gem Project covered a wide range of topics. I learned how Ruby manages Code Hierarchies, wrote complex nested logic in a CLI Controller, and built complex extraction methods to Scrape and Iterate through Data Structures. 

The CLI Controller and Scraper Service Object were most difficult components of `what_to_watch`.  I made ample use of `pry` within the Console while testing the Control Flow and determining the right dialogue with the user. While it took hours of experimenting on IMDB.com to find an efficient way to extract details for a movie title extracted from another site, I gained a good understanding of query selectors, URL Encoding, and debugging with Pry. 

At some point, I'd like to expand the function of the application and allow it to cover a wider range of streaming recommendations. `what_to_watch` could allow a user to then filter those results by genre and  return snippets of reviews along with recommendations. The Gem could display a simple high/low review score range for each recommendation and  search for and extract information on the cast or filming locations upon user request. 


---------

## Links

[The gemfile on rubygems.org](https://rubygems.org/gems/what_to_watch)

[The project repository on github](https://github.com/NotoriousCottonball/what_to_watch)























