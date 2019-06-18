---
layout: post
title:      "CLI Data Gem Project Blog"
date:       2019-06-18 12:51:07 -0400
permalink:  cli_data_gem_project_blog
---


Starting with this project I had many ideas on what webpage I could scrape and how my ***CLI Gem*** would look like but what I did not expect that the reality would hit me at the start. Coding for only 2 months my reality was that I did not  have many tools under my belt and my knowledge was to a sense limited. First website I wanted to scrape looked perfect at first but after going deeper into *HTML* I saw that the elements were loaded via *JavaScript*. When I ran *Inspect* on the page I would be able to see all the information I needed to scrape but when I would run *Page Source* that information was missing, same element was completely different. Same situation happened after I started scraping, info was not there. 

Point of this little story was getting to know myself, knowledge I had and how much more I needed to learn. 

I decided to use a webpage where I felt confident enough that I could finish the project combing all the things I learned and still being able to learn more without overstretching myself and my little tool belt.

The project is based on **Object Oriented Ruby** with an emphasis on scraping data from a URL.  For everybody reading this I would strongly recommend to build strong foundations on the lessons preceding this project and get a good understanding on how *Objects* and *Classes* work and be confident with the use of  *Iteration*, *Enumerators*, *Logic* and *Conditionals*. Important lession I learned while creating this project is that you can't build a house if you don’t know how to „hammer an nail“. More about that later.

Having a good understanding of mentioned subjects we can start the project. Like every project we need to make a plan on how, where and why. I decided to go with a *CLI Gem* called ***BestOnlineCodingBootcamps*** that would give the user information we all probably searched and looked for once, at least before joining Flatiron. 

The project would offer 2 levels of data, first level would list all bootcamps in a numerical order and info about the bootcamp name, rating and subjects. Second level would list the selected bootcamp webpage, locations, programs it offers and overall summary.

## How? Where? Why?
 
This is where the drama starts.. Or a fairytale (remember fundamentals :D). I won’t be explaining the whole project but will cover the most important parts.

When I started the project first thing I did is I created a mockup *CLI Class* that would give me a sense on what I need and how it could look in terminal, what the user would see. There would be a method called *beginning* that would ‘puts’ strings to the terminal so the user can see the process of the Gem and follow the instructions. Second method would be the main logic container method called *start* that would use all other Classes and operate on their data with heavy use of iteration and conditionals. 

Very beginning of the CLI Class:

```
class BestOnlineCodingBootcamps::CLI
  
  def call
    beginning  
    start
  end
	
 def beginning 
    puts ""
    print "Welcome to the 2019 Best Online Coding Bootcamps list by: ".yellow
    puts "Switchup".upcase.red
    puts ""
    puts "Enter '1' if you would like to see the list.".yellow 
  end
end
```
After expanding on that idea and everything that I could output to the terminal the main part started. 
Referring back to building a house, if the mockup *CLI Class* was a blueprint on how the house would look at the end, to start building a house we need the materials first. To get materials we need we will scrape our URL and save all that data somewhere to use later.
This is where our tools come in handy, having so much material in front of us it is time to be…

![bob](https://myhero.com/images/guest/g199393/hero45760/g199393_u51345_Bob_The_Builder.JPG)

Yaaay!! :D

Let’s take a look at the *BestOnlineCodingBootcamps:Scraper Class* and how we scrape the first level using enumerator ‘each’. 

```
require 'open-uri'
require 'nokogiri'
class BestOnlineCodingBootcamps::Scraper

  def self.scrape_index_page
    html = open("https://www.switchup.org/rankings/best-online-bootcamps")
    doc = Nokogiri::HTML(html)
    
    doc.css("li div.ranking-item").each do |camp|
        name = camp.css("div.ranking-item__header--left h3 a").text
        rating = camp.css("div.ranking-item__header--left span.ranking-item__rating--value").text.split.join(' ')
        subjects = camp.css("table tr:nth-child(3) td").text.split.join(' ')
        url = camp.css("div.ranking-item__header h3 a").attribute("href").value 
        BestOnlineCodingBootcamps::Bootcamps.new(name,rating,subjects,url)
    end
  end  
end
```

Here we have a Class method called *scrape_index_page*.
This method uses HTML parser ‘*nokogiri*’ and a HTTP wrapper ‘*open-uri*’ to get our website HTML and save tha data to a variable ‘**doc**’.
Then we use the ‘each’ enumerator to iterate through doc.css(“li div.ranking-item”), that is our container with all bootcamps, and then we assign a specific camp.css to new variables, variables that we will use in our  *BestOnlineCodingBootcamps:Bootcamps* class. 
Very important part is the line of code with **BestOnlineCodingBootcamps::Bootcamps.new(name,rating,subjects,url)**. Lets take a look first at the Bootcamps Class.

```
class BestOnlineCodingBootcamps::Bootcamps
    attr_accessor :name, :rating, :subjects, :url, :about, :website, :locations, :programs, :info

  @@all = []
  
  def initialize(name,rating,subjects,url)
    @name = name
    @rating = rating
    @subjects = subjects
    @url = url
    @@all << self
  end
end
```
In this *BestOnlineCodingBootcamps:Bootcamps* Class there is a method called ‘**initialize**’ that takes arguments and assigns them to instance variables. During initialization this method saves newly created Objects using ‘*self*’ and saves them to an empty class variable **@@all**.
This is why that line of code in our previous class is important.


**BestOnlineCodingBootcamps::Bootcamps.new(name,rating,subjects,url)** calls ‘**initialize**’ method from *BestOnlineCodingBootcamps::Bootcamps* Class, creates a new Object (bootcamp) using method ‘***new***’, takes the newly created variables that we scraped and uses them as arguments upon initialization.
Meaning now we have a new bootcamp with its info (name,rating,subjects,url) saved to an array.

```
[1] pry(#<BestOnlineCodingBootcamps::Bootcamps>)> @@all
=> [#<BestOnlineCodingBootcamps::Bootcamps:0x000000000243ad08
  @name="App Academy",
  @rating="4.7/5",
  @subjects=
   "JavaScript, Ruby, Rails, React.js, Redux, CSS, HTML, SQL, Advanced Algorithims",
  @url="/bootcamps/app-academy">]
```

To sum up:
*  we have a basic idea how our CLI class could look and that its responsibility
*  we explained how first level scraping looks and how we create a new Object (bootcamp)
*  lastly we explained what happens when that new object gets created

There are a lot more things in this project that I could cover like scraping the second level, logic for the CLI Class but I wanted to cover the basics and give an idea how to start by knowing the simple requirements that we need to pass in order to get us going. After that who knows what you can accomplish.

*“Things are only impossible until they are not.” *
― Jean-Luc Picard


