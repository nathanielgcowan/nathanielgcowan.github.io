---
layout: post
title:      "CLI Project Blog"
date:       2020-08-08 23:04:43 -0400
permalink:  flatiron_blog_cli
---





After pondering different ideas about which website I would like to use for my CLI project I settled on Austin Swing Syndicate, a local organization that hosts classes year round.

My objective was to help people pick which classes to go to by providing the description and date upon input of a chosen class from a list of data.

The website’s events page neatly list each of the different classes a guest can pick from. Further down you can find descriptions and dates for each class.

My program consists primarily of two parts. First, a command-line interface, which allows the user to interact with the computer by typing commands. Second, a scraper of the Austin Swing Syndicate events page.

I studied the layout and liked what I say so I started planning the layout for my CLI lib folder, which consist the following:

```
- Cli.rb
- Description.rb
- Parties.rb
- Scraper.rb
- Version.rb
- Environment.rb
```

After reading the instruction and following the video guide, I made the gem:

```
bundle gem austin_swing_syndicate_dance_classes
```


This provided me with the base for my bin folder, lib folder, as other needed files for my Ruby gem.

The first change I made was to add my file austin-swing_syndicate_dance_classes to my bin folder, to set up my CLI to display the call method.

```
#!/usr/bin/env ruby

require_relative '../lib/environment.rb'

AustinSwingSyndicateDanceClasses::CLI.new.call
```


Next I renamed an auto-created field within my lib folder to environment.rb, because I only intend to use this file to configure things for my application.

```
require_relative "./austin_swing_syndicate_dance_classes/version"
require_relative "./austin_swing_syndicate_dance_classes/cli"
require_relative "./austin_swing_syndicate_dance_classes/party"
require_relative "./austin_swing_syndicate_dance_classes/description"
require_relative "./austin_swing_syndicate_dance_classes/scraper"

require 'pry'
require 'nokogiri'
require 'open-uri'
require 'colorize'

module AustinSwingSyndicateDanceClasses
  class Error < StandardError; end
end
```

Once done I ran my code to see if my program was accepting my newly created files, but I was met with the following error:

```
bash: ./bin.austin-swing_syndicate_dance_classes: No such file or directory
[21:32:09] (master) austin_swing_syndicate_dance_classes
```


So I run chmod +x on file **./bin/austin-swing_syndicate_dance_classes** so that it would properly executed. Success!

Then within my **gemspec** file I  required the use of pry, Nokogiri, and open-uri, so my gem would work properly.

```
  spec.add_development_dependency "bundler", "~> 2.0"
  spec.add_development_dependency "rake", "~> 10.0"
  spec.add_development_dependency "pry"
  spec.add_dependency "nokogiri"
```

Then I ran bundle install and my application was set up properly. It was now time to go ahead and start fleshing out the gem. I started first with my **cli.rb** file.

**Cli.rb**

This file is the meat and potatoes of the program. This is the file that displays the program’s logic and contains that code that takes the user input.

The following are my methods:
/bullet the method
/explain what it does
* **Call**
* Puts welcome message
* Calls methods get_party, list_party, and show_answer
  
* **Get_party**
* Establishes instance variable @parties set to the file AustinSwingSyndicateDanceClasses::Party.all

* **List_party**
* Puts a message with instructions.
* Makes the my_statements array
* Applies map to @parties
* Push the name of a party into the my_statements array
* Sorts the my_statements array

* **Show_answer**
* Takes a user’s input and makes it an integer.
* Defines a valid input.
* Show the correct answer once received valid input.
* If invalid input, display error message then ask to try again.

* **Valid_input**
* Sets a valid input as an input integer less than the length of data and the integer is greater than zero.

* **Give_info_for**
* This method is the correct choice answer.
* Puts “Awesome choice!”
* Give the party description and date.


```
class AustinSwingSyndicateDanceClasses::CLI
  
  def call
                                                         
    puts "Hello and welcome to Austin Swing Syndicate. We encourage others to learn how to swing dance."
    get_party
    list_party
    show_answer
  end
  
  def get_party
    @parties = AustinSwingSyndicateDanceClasses::Party.all
  end
  
  def get_options
    
  end
  def list_party
    puts "Here are the available parties. Choose one below to see more information?".blue
    my_statements = []
    @parties.map do |party|
      my_statements << "#{party.name}"
    end
    my_statements.sort.each.with_index(1) do |i, index|
      puts "#{index}. #{i}"
    end
  end
  
  def show_answer
    chosen_party = gets.strip.to_i
    if valid_input(chosen_party, @parties)
      give_info_for(chosen_party)
    else
      puts "ERROR!!! Invalid input, please try again.".red
      list_party
      show_answer
    end
  end
  
  def valid_input(input, data)
    input.to_i <= data.length && input.to_i > 0
  end
  
  def give_info_for(chosen_party)
    party = @parties[chosen_party- 1]
    party.description
    puts "Awesome choice!Feel free to read more about #{party.name} Would you like to look at another?".green 
    puts party.description
    puts party.date
    puts "Would you like to see another? Yes or No"
      want_to_see_more = gets.strip
      if "yes"
      list_party
      show_answer
    end
  end
end
```


**Description.rb**

This file sets the arguments for the descriptions of my program.

The methods are as follows:

* **Initialize**

* Initializes arguments name and party
* Calls add_to_party and save method
 
* **Self.all**
 
* Defines the all class variable.(That is set to an empty array)
 
* **Add_to_party**
 
* Pushes the new self description into the party instance variable unless it would be a duplicate.
 
* **Save**

* Pushes the given self in the all class variable.

```
class AustinSwingSyndicateDanceClasses::Description
  attr_accessor :name, :party
  @@all =[]
  
  def initialize(name, party)
    @name = name
    @party = party
    add_to_party
    save
  end
  
  def self.all
    @@all
  end
  
  def add_to_party
    binding.pry
    @party.descriptions << self unless @party.descriptions.include?(self)
  end
  
  def save
    @@all << self
  end
end
```

**Party.rb**

This file sets the arguments and array for the individual classes.

The methods are as follows:

* **Initialize**
* Sets the instance variables name, description, and date.
* Calls the save method

* **Self.all**
* Class variables take in the scrapers if the all class variable is empty.
* Returns the all class variables(set at the top of the code to an empty array)

* **Save**
* Pushes self into the all class variable.

```
class AustinSwingSyndicateDanceClasses::Party
  attr_accessor :name, :description, :date
  @@all =[]
  
  def initialize(name,description, date)
    @name = name
    @description = description
    @date = date
    save
  end
  
  def self.all
    AustinSwingSyndicateDanceClasses::Scraper.scrape_parties if @@all.empty?
    @@all
  end
  
  def save
    @@all << self
  end
end
```


**Scraper.rb**

This file is the scraper which is what takes the data from the webpage.

It’s methods are as follows:

* **Self.scrapre_parties**
* Uses Nokogiri to parse the uri.
* Takes the css for “div.slide-content”.
* Uses each to retrieve the text for name, description, and date.

```
class AustinSwingSyndicateDanceClasses::Scraper
  def self.scrape_parties
    doc = Nokogiri::HTML(open("http://austinswingsyndicate.org/events/"))
    parties = doc.css("div.slide-content")
    parties.each do |n|
        name = n.css("h3.slide-entry-title.entry-title a").text
        description = n.css("div.slide-entry-excerpt.entry-content").text
        date= n.next_element.css("div.slide-meta time").text
        AustinSwingSyndicateDanceClasses::Party.new(name,description,date)
    end
  end
end
```

**Verson.rb**

This file defines the version of the gem.

```
module AustinSwingSyndicateDanceClasses
  VERSION = "0.1.0"
end
```

**Environment.rb**

These are the files that configure the run-levels of my application.

```
require_relative "./austin_swing_syndicate_dance_classes/version"
require_relative "./austin_swing_syndicate_dance_classes/cli"
require_relative "./austin_swing_syndicate_dance_classes/party"
require_relative "./austin_swing_syndicate_dance_classes/description"
require_relative "./austin_swing_syndicate_dance_classes/scraper"

require 'pry'
require 'nokogiri'
require 'open-uri'
require 'colorize'

module AustinSwingSyndicateDanceClasses
  class Error < StandardError; end
end
```

**Austin-swing_syndicate_dance_classes.gemspec**

I then wrote my gemspec files to attribute my RubyGem.
If you want to check out my README.md, feel free to use the link to my code on GitHub below.

```
lib = File.expand_path("../lib", __FILE__)
$LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)
require "austin_swing_syndicate_dance_classes/version"
Gem::Specification.new do |spec|
  spec.name          = "austin_swing_syndicate_dance_clases"
  spec.version       = AustinSwingSyndicateDanceClasses::VERSION
  spec.authors       = ["'nathanielgcowan'"]
  spec.email         = ["'nathaniel.g.cowan@gmail.com'"]
  spec.summary       = %q{This is my short summary of my gemspec. I am writing one because I am supposed to do so. This is what is written down and what I know to put here.}
  spec.description   = %q{This is the description of It is meant to help people in Austin meet others that want to learn swing dancing.}
  spec.homepage      = "https://github.com/nathanielgcowan/learn-co-sandbox/tree/master/austin_swing_syndicate_dance_classes"
  spec.license       = "MIT"
"http://austinswingsyndicate.org/"
  spec.files         = Dir.chdir(File.expand_path('..', __FILE__)) do
    `git ls-files -z`.split("\x0").reject { |f| f.match(%r{^(test|spec|features)/}) }
  end
  spec.bindir        = "exe"
  spec.executables   = spec.files.grep(%r{^exe/}) { |f| File.basename(f) }
  spec.require_paths = ["lib"]
  spec.add_development_dependency "bundler", "~> 2.0"
  spec.add_development_dependency "rake", "~> 10.0"
  spec.add_development_dependency "pry"
  spec.add_dependency "nokogiri"
end
```

Here’s my Code on [GitHub](httphttps://github.com/nathanielgcowan/learn-co-sandbox://).
Also, feel free to check out [Austin Swing Syndicate](hhttp://austinswingsyndicate.org/events/ttp://)’s Event Page.






