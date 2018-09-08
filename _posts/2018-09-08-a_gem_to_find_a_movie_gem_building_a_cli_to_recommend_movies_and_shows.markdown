---
layout: post
title:      "A Gem to Find a (Movie)Gem: Building a CLI to Recommend Movies and Shows"
date:       2018-09-08 04:39:03 -0400
permalink:  a_gem_to_find_a_movie_gem_building_a_cli_to_recommend_movies_and_shows
---


My first Flatiron School project involved creating a Ruby Gem with a Command Line Interfact(CLI) and user input requested access to data from a web page. The project required a strong grasp of Ruby Gem Code Hierarchies, Ruby's `$LOAD_PATH`, the parsing capabilities of the Nokogiri Gem, and Ruby Object Relationships. The application had to exhibit strong Object Oriented Programming design patterns and efficiently extract and display data that is kept updated at an external source. 

#### I. The `what_to_watch` Ruby Gem


`what_to_watch` is a Ruby Gem that returns best-reviewed and recently_added movies/television based on a user's access to popular streaming services. The application asks the user to respond Yes or No (y/n) to whether they have access to Netflix, Amazon Prime, HBO, Hulu, or Showtime. It then asks if they'd like to see recommendations for Best Movies, Best TV Shows, or Recently-Added Movies/TV. 

The application then scrapes the Vulture.com/streaming page according to user input. It returns a filtered list of shows, designating those available and unavailable to the user, and indicating the streaming service at which the user could watch them. 

The user may select an item from the returned list by number, upon which the application will search IMDB.com for the item and display details including a description, the genre, the year, the director/creator/writers/stars, and IMDB item page url. 

The user may then select a different item from the original returned list, return to the main menu to see a different set of recommendations, or exit the program. CLI controller allows the user to exit at any time by simply typing 'exit' or 'Exit.'

Vulture.com's Streaming Hub provides an excellent source of recommendations based on very thorough and in-depth ratings/reviews. IMDB.com provides a repository of movie/television information that is searchable and easy to scrape. 

The first is somewhat hard to scrape and get details from, but provides an up-to-date source of movie/television streaming recommendations that are the result of a rigorous review and ratings process. The latter easily provides key details/additional information. 

The idea of `what_to_watch` is to sort (by streaming_service) and filter (by best-reviewed-movies, best-reviewed-television, or recently-added-movies/tv) the results of Vulture.com's rating/review system and use IMDB.com to fill in the relevant details. 

The application uses the `:title` attribute of the user's selection from the returned list of items to generate a search results page on IMDB. It then scrapes that page, since the closest search result's 

A couple days after I finished a working version of  `what_to_watch`, Vulture.com/streaming reformatted their homepage layout. Unfortunately, my application no longer worked since my Scraper method relied on the previous HTML and a completely different Document Object Model structure.  With live pages, a slight change to the nesting or the renaming of a class can break a scraping script.

However, rewriting the Scraper method for Vulture.com/streaming actually enhanced the application since it now draws on much more structured HTML with patterns that will likely remain consistent update after update. I tried to make the most of the new architecture, refactoring code in the CLI and Class Models to ensure the ease of slight readjustments in the future. 

#### II. Difficulties / Lessons Learned






