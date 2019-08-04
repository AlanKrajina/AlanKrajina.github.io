---
layout: post
title:      "Projects Track App"
date:       2019-08-04 06:51:29 -0400
permalink:  projects_track_app
---


For this project I decided on making an app that allows users to CRUD projects and to keep track of them, using Sinatra. 
The purpose of the App would be like a dashboard where a User can save and update his Projects or any tasks available.
I will go through the requirements to explain how the app works.


**Project Requirements**

Build an MVC Sinatra application.

* Models: The 'logic' of a web application. This is where data is manipulated and/or saved. In the app there are three models: User, Project and Result, including their associations.

* Views: The 'front-end', user-facing part of a web application - this is the only part of the app that the user interacts with directly.  Views contains index and layout erb-s and specific folders for the User and Project where we have the abillity to manipulate data and create, read, update and delete.
 
* Controllers: The go-between for models and views. The controller relays data from the browser to the application, and from the application to the browser. Controllers implemented in the app are application_controller, users_controller, projects_controller and results_controller.

Use ActiveRecord with Sinatra.
* In this project ActiveRecord's built-in ORM methods have a crucial part. Active Record is a Ruby gem, meaning we get an entire library of code just by running gem install activerecord or by including it in our Gemfile.

* Once our Gem environment knows to put ActiveRecord into the picture, we need to tell ActiveRecord where the database is located that it will be working with. 

* We do this by running ActiveRecord::Base.establish_connection. Once establish_connection is run, ActiveRecord::Base keeps it stored as a class variable at ActiveRecord::Base.connection

```
ActiveRecord::Base.establish_connection(
  :adapter => "sqlite3",
  :database => "db/#{ENV['SINATRA_ENV']}.sqlite"
)
```

Use multiple models.
* Active Record is the M in MVC - the model - which is the layer of the system responsible for representing business data and logic. Active Record facilitates the creation and use of business objects whose data requires persistent storage to a database. It is an implementation of the Active Record pattern which itself is a description of an Object Relational Mapping system.

* Models included in the app are user.rb, project.rb, result.rb

Use at least one has_many relationship on a User model and one belongs_to relationship on another model.
```
class User < ActiveRecord::Base
  has_secure_password
  has_many :projects
end

class Project < ActiveRecord::Base
  belongs_to :user
  has_many :results
end

class Result < ActiveRecord::Base
  belongs_to :project
end
```

Must have user accounts - users must be able to sign up, sign in, and sign out.
* Some of the methods to get this functionality accomplished within the users_controller:

```
  get '/signup' do
      if !logged_in?
          erb :'/users/create_user'
      else
          redirect '/projects'
      end
  end
  
  post '/signup' do
    if params[:username] == "" || params[:email] == "" || params[:password] == ""
      redirect to '/signup'
    else
      @user = User.new(:username => params[:username], :email => params[:email], :password => params[:password])
      @user.save
      session[:user_id] = @user.id
      redirect to '/projects'
    end
  end 
```

Important "helper" methods.
```
  helpers do
    def logged_in?
      !!session[:user_id]
    end

    def current_user
      User.find(session[:user_id])
    end
  end
```
Validate uniqueness of user login attribute (username or email).
*  Code that checks input and compares all created Users and their username & email to input params that were made to create a new User

```
User.all.any?{|user|user.email.downcase == params["email"].downcase||user.username.downcase == params["username"].downcase} 

```

Once logged in, a user must have the ability to create, read, update and destroy the resource that belongs_to user.
* CRUD is an acronym for the four verbs we use to operate on data: Create, Read, Update and Delete. Active Record automatically creates methods to allow an application to read and manipulate data stored within its tables.

* CRUD functionality is implemented in projects_controller where a User has the ability to CRUD his own Projects. Project belongs_to User.

Ensure that users can edit and delete only their own resources - not resources created by other users.
* Bellow is the main part of the code that first checks if the user is logged_in? (   !!session[:user_id]  ), then it uses ActiveRecord method  "find_by_id(params[:id])" and evaluates on second IF conditional that the instance of a project and the project.user are the same as the current_user (  User.find(session[:user_id]   )


```
  get '/projects/:id/edit' do
    if logged_in?
      @project = Project.find_by_id(params[:id])
      if @project && @project.user == current_user
        erb :'projects/edit_project'
      else
        redirect to '/projects'
      end
    else
      redirect to '/login'
    end
  end
```

Validate user input so bad data cannot be persisted to the database.

* Example of bad data during sign in where a new User cannot be created if some of the input fields are left empty:

```
  post '/signup' do
    if params[:username] == "" || params[:email] == "" || params[:password] == ""
      redirect to '/signup'
    else
      @user = User.new(:username => params[:username], :email => params[:email], :password => params[:password])
      @user.save
      session[:user_id] = @user.id
      redirect to '/projects'
    end
  end 
```

BONUS: Display validation failures to user with error messages. 

* Inside projects_controller:

```
  get '/projects/new' do
      @message = session.delete(:message)
			...
```

And validation failure:

```
  post '/projects' do
        session[:message] = "Please fill out all of the fields."
				...

```
  
