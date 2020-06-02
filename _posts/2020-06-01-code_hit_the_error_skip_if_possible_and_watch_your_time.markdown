---
layout: post
title:      "Code, hit the error, find a workaround, and watch your time"
date:       2020-06-01 00:01:49 -0400
permalink:  code_hit_the_error_skip_if_possible_and_watch_your_time
---


I loved working on Rails project, as I come across rails code quite often at work.

My biggest challenge in working with this project was time and my own stubbornness. When I come across an issue, I can't move on, until I resolve it and it's almost impossible for me to give up. Well, lesson learned that's a bad habit.

While working on my project I got an error while trying to use `find_or_create_by` and I'll tell you about it in this post.

So as one part of my project, I had to use nested forms that require a class to accept nested attributes and separate writers for those attributes. As I was building my app (movie_db), my main class Movie had 3 belongs_to associations and each of those required nested_attributes.

```
class Movie < ApplicationRecord

  belongs_to :genres
  belongs_to :director, class_name:"Person", optional: true
  belongs_to :writer, class_name:"Person", optional: true
```

It's always so much easier to build code, based on something you've used or seen before. But I wasn't fortunate enough since I didn't come across an example of writer method for nested_attributes for belong_to association.
 



I first started by building nested attributes in the form:
```
<tr>
  <td><h3>Genres:</h3></td>
  <td><%= f.fields_for :genre do |genre_attributes|%></td>
    <td><%= genre_attributes.label :id, "Genre"%></td>
    <td><%=  genre_attributes.collection_select :id, @genres,:id,:name, include_blank: true %></td>
  <td>Or create a new genre:</td>
  <%= genre_attributes.label :name, "Genre Name:" %><td>
    <td><%= genres_fields.text_field :name %></td><%= genre_attributes.text_field :name %>    
  <% end %>
    </td>
    </tr>```

Based on that form I was building custom writer for ```genre_attributes``` and tried using ```find_or_create_by``` and it looked like this:
```
def genres_attributes=(genres_attributes)
     if genres_attribute["name"].present?
        genre=Genre.find_or_create_by(genres_attributes)
        self.genres<genre
     end
end
```
My logic for the writer was pretty simple if the new Genre name field had any information, use ```find_or_create_by``` to find existing Genre in case the user didn't realize it already exists or creates a new one. Create part worked like a charm and new Genre was created and dynamically assigned to the movie, but it could never find the existing genre and I was hitting an error or creating duplicate records. Side note: duplicates in the database drive me crazy! I struggled for hours trying to find a way to make my code work, and all that time yielded ```nil``` results. Eventually, I decided that instead of wasting time on figuring this mess out, I would find a workaround. 
I did some research and figured the easiest way around my problem would be to change the movie genre relationship and add a joint table. So I went ahead and created ```movie_genres``` table and in my movie class removed ```belongs_to :genres``` and added 
```
has_many :movie_genres
has_many :genres, through: :movie_genres
    ```
From there it was simple few tweeks to the form:

```
<tr>
  <td><h3>Genres:</h3></td>
  <td><%= f.select :genre_ids, options_from_collection_for_select(@genres, :id, :name, f.object.genre_ids), {}, {:multiple => true} %></td> 
  <td>Or create a new genre:</td>
  <%= f.fields_for :genres, movie.genres.build do |genres_fields| %>
    <td><%= genres_fields.text_field :name %></td>
  <% end %>
</tr>
```

and genres_attributes writer

```
def genres_attributes=(genre_attributes)
   genre_attributes.values.each do |genre_attribute|
     if genre_attribute["name"].present?
       genre = Genre.find_or_create_by(genre_attribute)
      # self.genres << genre
       self.movie_genres.build(genre: genre)
     end
   end
 end
```
This code worked, and I had no issues with ```find_or_create_by``` here. I honestly don't know the right answer, but it seems ```find_or_create_by``` doesn't like ```belongs_to``` relationships.

Well, I was happy and ready to move on to the next step of new movie form, as hit the same roadblock, nested attributes that require custom writer, and it should make sense to use ```find_or_create_by```. Well, I tried but got the same problem as before, yes to create, no to find.

This time, I knew I need a workaround and  I found it. I broke ```find_or_create_by``` in 2 pieces and used ```find_by``` and then ```build``` methods. Along the way, I actually created a separate method for ```person_find_by(attributes)``` and used it in multiple places. But custom writer looked like this:

```
 def director_attributes=(director_attribute)
   if director_attribute[:name].present?
     if person=person_find_by(director_attribute)
       self.director=person
     else
      self.build_director(director_attribute)
     end
   end
 end
```

That worked, and I didn't waste hours trying to figure out why ```find_or_create_by``` didn't work. Hopefully one day, I'll know the answer why it didn't work, but in the end, my app is working without it.

When coding, if you can't figure something out, don't spend hours on it, make it work first, and then fix it later, but don't stop on 1 thing and don't waste hours as i did.





