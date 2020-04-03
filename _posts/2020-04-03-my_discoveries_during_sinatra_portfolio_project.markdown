---
layout: post
title:      "My Sinatra Project discoveries"
date:       2020-04-03 01:18:08 -0400
permalink:  my_discoveries_during_sinatra_portfolio_project
---


## Thank you Brian Emory!
When I was planning my Sinatra Project, I was pretty confident in my idea (based on my work experience). I didn't have any issues with Model-View-Controller (MVC) structure, as I already had a picture of my database in my mind. But there was some things that I was worried about (environment,config). Just before i started the project I learned that simple line of code will create complete Sinatra template with all files in the right places.

```
gem install corneal
```
With this line all magic starts, and you can learn more by visiting - 'https://thebrianemory.github.io/corneal/'.
Once you install gem, create your app and run bundle, I would suggest using `corneal scaffold NAME` command. It's creates model and adds views and controllers for that model. 
Thank you Brian Emory for creating this super useful Gem.

## Slug
Each record in database table has unique ID number, but it's not the most convienient way to identify records by just a number. It would be way better to have something more readable. Therefore in my database tables have at least one more unique column.
![]([img]\https://i.imgur.com/MgFaooN.png[/img])
instructors, courses & course_groups tables have 1 common column - name. 
'http://localhost:9393/instructors/anna' looks and feels a lot better than 'http://localhost:9393/instructors/1', right?
In the course we've learned about [slug](https://itnext.io/whats-a-slug-f7e74b6c23e0?gi=5bb7951d1921) - human-readable, unique identifier. So if you check this url -  'http://localhost:9393/instructors/anna' - anna is a slug.
How do we get it and how do we access records with slug, it's not part of the database?
1. How do we create a slug? - we can use the method below
```
def slug
    name.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  end
```
it will turn name value into slug (i.e. Nick Solony => nick-solony)
2. How do we find object by slug?
```
def find_by_slug(url)
    find {|object| object.slug == (url)}
end
```
These methods can be added to each model, but in order to keep code DRY it's best to create a module in concerns folder and use it in each model. So in order for me to be able to re-use same modules in all of my models, I used same column name - "name".
While writing this post, I actually found a slugify gem - 'https://rubygems.org/gems/slugify/versions/1.0.7'. Feel free to explore it.

## CRUD / delete/ a href
While working on navigation in my app, I've encountered following problem, I used `a href` links to navigate my app and to access edit function. When I tried to do the same for delete, my app broke.
So I started looking into this issue.
Lets break it apart:

```
<a href="/instructors/<%=instructor.slug%>/edit"><%=instructor.last_name%>, <%=instructor.first_name%></a>
```

if we click on the link created by code above it would reach into GET method ```get "/instructors/:slug/edit"```.
However, delete requires DELETE method and it can't be accessed via GET method.

Therefore, I had to switch all of my edit links to be buttons.
```
<button class ="button" onclick="window.location.href='/instructors/<%=@instructor.slug%>/edit'">Edit Profile</button>
```
and used delete form in order to ge to DELETE method

```
<form action="/instructors/<%=@instructor.slug%>/delete" method="post">
      <input type="hidden" name="_method" value="DELETE">
      <input class = "button" type="submit" value="Delete Instructor">
  </form>
```

## Conclusion
Each new project brings new chalenges, but they allow me to practice an art of Google-fu. As I was once told, don't try to invent the wheel, 99% of things already exist you just need to know how to find it.
So I think it's important to learn how to find answers: ask friends, mentors, google!




