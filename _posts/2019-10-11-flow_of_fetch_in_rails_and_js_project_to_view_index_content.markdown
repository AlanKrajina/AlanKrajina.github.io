---
layout: post
title:      "Flow of fetch in Rails&JS Project to view Index content"
date:       2019-10-11 10:28:36 -0400
permalink:  flow_of_fetch_in_rails_and_js_project_to_view_index_content
---


First thing we need to make sure that our backend is set up and our routes and associations connected.

In this walkthrough we create Lesson resource using:

```
rails g resource Lesson title:string content:string comment_id:integer
```

Comment:

```
rails g resource Comment text:string
```

Rating:

```
rails g resource Rating integer:stars lesson_id:integer
```

After that we run:

```
rake db:migrate
```

to create shema that would look like this:

```
ActiveRecord::Schema.define(version: 2019_10_08_091954) do

  create_table "comments", force: :cascade do |t|
    t.string "text"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

  create_table "lessons", force: :cascade do |t|
    t.string "title"
    t.string "content"
    t.integer "comment_id"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

  create_table "ratings", force: :cascade do |t|
    t.integer "stars"
    t.integer "lesson_id"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end
end

```

Associations in models look like this:

```
class Comment < ApplicationRecord
    has_many :lessons
end

class Lesson < ApplicationRecord
    belongs_to :comment
    has_many :ratings
end

class Rating < ApplicationRecord
    belongs_to :lesson
end

```

Then we go to our Lesson controller and add:

```
    def index
        @lessons = Lesson.all
        respond_to do |f|
          f.html
          f.json {render json: @lessons}
        end
    end

```

With this we get a json representation of all Lessons (render) and allow access to html and json data. The way we check the return value would be by running:

```
http://localhost:3000/lessons.json 
```

That would return an ARRAY of OBJECTS (lessons we created before). Every object we get back will be an instance of a Lesson with each having its own attributes like title, content, comment and ratings (plural- has_many).

One thing to be aware is that other actions like New, Create, private lesson_params are added correctly in the same controller.

To finish up the backend part we need to add

```
gem 'active_model_serializers'
```

to our gemfile and bundle install.

To create serializer folder to work with run:

```
rails g serializer Lesson
```

After that is done we add attributes we want to work with and see in json plus we add associations to the Lesson serializer:

```
class LessonSerializer < ActiveModel::Serializer
  attributes :id, :title, :content,:comment_id

  belongs_to :comment
  has_many :ratings
end

```


The reason why we are adding associations here are the same as why we are adding same ones in Rails model, to be able to use build-in methods and get access to associated data. 


**Now for the frontend part.
**

The behaviour we want is that whenever we click on our Index link on the homepage we don't open a new page with all lessons but we want to repaint the DOM using Ajax fetch.



We do this by "hijacking" CLICK on a link that we added to our homepage. To do that we add a html link that displays all lessons(index link), that is essentially same as we have for Rails index:

```
        <%= link_to 'All Lessons', lessons_path, class: "all_lessons" %>
```

- VERY IMPORTANT : add a class to the link and remember the name of the class. We will use that class in JS section.


Now we create our JS file where we do all of the magic:

```
assets/javascripts/lessons.js
```

In our .js file first we make a document ready function:

```
$(() => {
    clickHandlers();
})

```

The ready() method is used to make a function available after the document is loaded. Whatever code you write inside the $(document ).ready() method will run once the page DOM is ready to execute JavaScript code.

Then we define our clickHandlers function:

```
const clickHandlers = () => {
    $('.all_lessons').on('click', e => {
        e.preventDefault()
        getLessons()
    })

```

Now comes the important part. Using jQuery we are selecting that particular element with a class "all_lessons" that we created before. 

.on method in jQuery takes two arguments, event we want to listen for and callback function we want to run  when that event happens (takes in event object „e“).

Using 

```
e.preventDefault() 
```

we prevent default when that link is clicked (meaning default will render via rails html markup).
On the last line we simply call getLessons() when our link is clicked.

For us to call that function we need to declare it:

```
  const getLessons = () => {
    fetch(`/lessons.json`)
      .then(res => res.json())
      .then(lessons => {
         lessons.forEach(lesson => {
           let newLesson = new Lesson(lesson)
  
           let lessonHtml = newLesson.formatIndex()
  
           $('#app-container').append(lessonHtml)
         })
      })
  }

```

Here we use 

```
fetch(`/lessons.json`)
```

to get lessons (backend API endpoint) as a response object(res).

Using

```
.then(res => res.json
```

we convert that data to ARRAY of OBJECTS and with the line


```
.then(lessons.. 
```

we have lessons as a manipulatable  Array we can work with.

Since lessons are now an array we can iterate and get individual object in array:

```
         lessons.forEach(lesson => {
```

Here on every iteration we create a new instance depending on how many lesson objects we have in our lessons array.

BUT!

We can't create a new instance without a constructor that will allow us to use line like this:

```
           let newLesson = new Lesson(lesson)
```

So!

Let's create our Lesson constructor:

```
  function Lesson(lesson) {
    this.id = lesson.id
    this.title = lesson.title
    this.content = lesson.content
    this.user = lesson.user
    this.comment = lesson.comment
    this.ratings = lesson.ratings
  };

```

This works in the same way as Class in Ruby meaning it takes an argument and assigns attributes from the argument to the instance being created.

So now every instance of a lesson we created from our json array has its own id,title etc..

Now this works:

```
           let newLesson = new Lesson(lesson)
```

Next line

```
           let lessonHtml = newLesson.formatIndex()
```

tells us that we are calling a

```
.formatIndex() 
```

function on our instance we created (newLesson).

Meaning we have to create Lesson „class“ method, in JS it is a prototype function:

```
  Lesson.prototype.formatIndex = function(){
    let postRatings = this.ratings.map(rating => {
      return (`
      *${rating.stars}
      `)
    })

    let lessonHtml = `
    <p>Lesson title:</p>
    <p>${this.title}</p>
    <p>Lesson content: </p>
    <p>${this.content}</p>
    <p>Lesson comment: </p>
    <p>${this.comment.text}</p>
    <p>Lesson Ratings: </p>
    <p>${postRatings}</p>
    `
    return lessonHtml
  };

```


Function is called formatIndex. As the name implies it formats the Index page. Simply it just creates new html elements that we will use to put on our lesson data on the page.



Now we go back to this line

```
           let lessonHtml = newLesson.formatIndex()
```

that adds all this html and connects it to the newLesson object so now we can use „this“ and give that html data like id, content, comment etc..


Last part is 

```
           $('#app-container').append(lessonHtml)
```

Here we add all that html with object data to a specific part on the dom. I use id #app-container.

To summarise what happens overall:

User clicks the index link on homepage:

```
        <%= link_to 'All Lessons', lessons_path, class: "all_lessons" %>
```


After clicking function getLessons() runs and it repaints the dom with our new html on a location we choose.

```
        <p id="app-container"></p>
```

The End

:)









