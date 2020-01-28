---
layout: post
title:      "Building my first CLI - daily_scores"
date:       2020-01-28 03:12:08 +0000
permalink:  building_my_first_cli_-_daily_scores
---


In my first project I had to scrape date from a website and create CLI inteface to allow users to control and receive information. As soon as I understood a task, I decided that I will work on soccer scores. I daily check scores myself, and I usually go to a website - livescores.com.

Well, so it was so simple to me, I decide I just need to scrape it and get this project of my task list. I started off by writing a brief readme description about how this app should work. In short it looked like this:
1. Scrape
2. List countries
3. Select country
4. List leagues
5. Select league
6. List games

That was first part of the project, I created a class Cli to be responsible for user interactions and upon it build 2 more classes (country and league). I was able to get country and league classes to work with each other and got to the trickies part of the project.

I had to scrape livescores.com, and that's where I hit my biggest road block, I've learned how to scrape html websites, but MY MISTAKE, I didn't check that livescores.com used javascript, which at that moment i didn't know how to scrape. I actually still don't know to scrape javascript sites, but I did spend few hours trying to get it to work. I found out that it can be done with PhantomJS and Watir. Well, I was able to get those in my code, BUT I got next message "support for PhantomJS has been deprecated". At this point I felt really upset, when I found PhantomJS and Watir I was so excited about learning and finding how to do something that I didn't learn yet, but nothing worked and I had to start looking for a new approach.

Eventually, I listned to a suggestion of my lead, and started looking for a new data source. I found it, and it was https://www.scorespro.com/. Code was all in html and I was able to scrape countries and leagues relatively easy.
However when I got to games, it was way harder because data needed for games was grouped in a table, so I had to use CSS class selectors within table cells, which was a little tricky `"td[@class='away winteam uc'] a"`. It took me some time to figure it out, and in order to make life a little easier, I actualy broke down table by rows and had to iterate through each row to get necessary data. As i was running test, I noticed that based on a result of the game, cell class would change, so I had to build an additional If statement to encounter for that variable.

Finally, when scraping was done. I was able to test my gem, and clean up to make it nice and functional.

You can check it out here - https://github.com/nicksolony/daily_scores and video overview is available - http://tiny.cc/daily_scores.

This was my first experience building Ruby gem on my own.
