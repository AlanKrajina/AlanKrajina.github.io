---
layout: post
title:      "PizzaOnRailsApp - Rails project"
date:       2019-09-06 13:55:02 -0400
permalink:  pizzaonrailsapp_-_rails_project
---


In this blog I will go through project requirements and try to explain the way they are meet.

**1.**

**Include at least one has_many, at least one belongs_to, and at least two has_many :through relationships
Include a many-to-many relationship implemented with has_many :through associations. The join table must include a user-submittable attribute — that is to say, some attribute other than its foreign keys that can be submitted by the apps user**

Associations are a set of macro-like class methods for tying objects together through foreign keys. Each macro adds a number of methods to the class which are specialized according to the collection or association symbol and the options hash. It works much the same way as Ruby's own attr* methods.

Example for these associations would be Pizza model:
```
class Pizza < ApplicationRecord
    belongs_to :user
    belongs_to :rating
    
    has_many :meats
    has_many :cheeses
    has_many :dips
    has_many :drinks
    has_many :toppings
end
```

Pizza belongs_to :user and belongs_to :rating, meaning it has been given a class method of Pizza.user and Pizza.rating and through these methods we can find User and Rating attributes.

User-submittable attribute would be has_many :meats where the App user is able to choose via checkbox what type of meat he wants to add to his pizza. The class method here would be Pizza.meats with the use of iteration like:
```
  Pizza.meats.each do |meat| 
      meat.name
  end 

```
The has_many :through associations are represented by the use of two other models, User and Rating.
```
class User < ApplicationRecord
    has_many :pizzas
    has_many :ratings, through: :pizzas
end

```
```
class Rating < ApplicationRecord
    has_many :pizzas
    has_many :users, through: :pizzas
end

```

With these associations User gets the ability to call User.pizzas.rating and Rating.pizzas.user using iteration on pizzas and returning back the value of Rating or User for the specific Pizza.

The way the connection is made is by using foreign keys that are added to the pizzas table upon creation using resource generator:

```
rails g resource Pizza name:string delivery_address:string delivery_notes:string user_id:integer rating_id:integer
```

Example of User has_many :ratings, through: :pizzas would be:
```
class PizzasController < ApplicationController
    
  def index
...
    @pizzas = User.find(params[:user_id]).pizzas
...

```
Here we find a specific user by his [:user_id]) and use associations class method to access and connect to all pizzas(has_many :pizzas). 

After that we save all of users pizzas to instance variable @pizzas.

The has_many :ratings, through: :pizzas is represented in Pizza index view: (part of the code)
```
@pizzas.each do |pizza| 
pizza.rating.stars 
pizza.rating.comment
end 

```
Here we iterate through the instance variable @pizzas we previously created and access stars and comment attributes from Rating.
In short User has many Pizzas and many Rating attributes through Pizza.

[https://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html](http://)

**2.**

**Your models must include reasonable validations for the simple attributes. 
**

**You dont need to add every possible validation or duplicates, such as presence and a minimum length, 
but the models should defend against invalid data.** 

Validations are used to ensure that only valid data is saved into your database. Model-level validations are the best way to ensure that only valid data is saved into your database. They are database agnostic, cannot be bypassed by end users, and are convenient to test and maintain. Rails makes them easy to use, provides built-in helpers for common needs, and allows you to create your own validation methods as well.

Some of validations in the App:

```
class User < ApplicationRecord
    validates :name,  uniqueness: true
..

```

This validation checks for a name input that the App user is submitting upon creating a new account. Uniqueness: true evaluates is the User name input unique, meaning a User with the same name doesn’t exist in the database of previously created Users and does not allow for a User with the same name to be created again.

[https://guides.rubyonrails.org/active_record_validations.html](http://)

**3.**

**Your forms should correctly display validation errors. 
**

**a. Your fields should be enclosed within a fields_with_errors class 
**

**b. Error messages describing the validation failures must be present within the view.
**

Here we can continue on the last requirement were validations were needed. 

The form with validation error would be:

```
<div class = "fields_with_errors"><%= render 'layouts/user_login_error' %></div>

<%= form_for @user do |f| %>
  <%= f.label :name , class: "a-primary"%>
  <%= f.text_field :name %><br />
  </br>
  <%= f.label :email , class: "a-primary"%>
  <%= f.text_field :email %><br/>
  </br>
  <%= f.label :password , class: "a-primary"%>
  <%= f.password_field :password  %><br/>
  </br>
  <%= f.submit %>
<% end %>
</div>

```

Here we use class = "fields_with_errors" that has custom attributes saved in application.css file:

```
.fields_with_errors{
    color:red;
    background-color:rgba(206, 184, 110, 0.6) ;
    margin-right: 80em;
}

```

The error message is shown in the users/new.html.erb with the use of partial containing the error from layouts/ _user_login_error.html.erb

```
<% if @user.errors.any? %>
  <div class = "fields_with_errors">
    <h2><%= pluralize(@user.errors.count, "error") %> prohibited this user from being created:</h2>
 
    <ul>
    <% @user.errors.full_messages.each do |msg| %>
      <li><em><font color="red"><%= msg %></font><em></li>
    <% end %>
    </ul>
  </div>
<% end %>

```

Not to forget in order for this validation failure to work we need to add the following to User model:

```
class User < ApplicationRecord
    has_secure_password

    validates :name,  presence: true
    validates :email, presence: true

```

**3.**

**Your application must be, within reason, a DRY (Do-Not-Repeat-Yourself) rails app.**

Some examples for this requirement:

```
class PizzasController < ApplicationController
       
  def show
    find_pizza
  end
        
  def edit
    find_pizza
  end

  def update
    find_pizza
    if @pizza.update(pizza_params)
      redirect_to pizza_path(@pizza)
    else
      render :edit
    end
  end

  def destroy
    find_pizza
    @pizza.destroy
    redirect_to user_path(current_user)
  end

  private

  def find_pizza
    @pizza = Pizza.find_by(id: params[:id])
    if !@pizza
      redirect_to pizzas_path
    end
  end

```

Here we see four Pizza controller actions using one method find_pizza.

And helper methods:

```
module ApplicationHelper
    def current_user
        @current_user ||= User.find_by_id(session[:user_id])
      end
  
      def logged_in?
        !!session[:user_id]
      end
end

```

[https://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html](http://)

[https://guides.rubyonrails.org/layouts_and_rendering.html#using-the-content-for-method](http://)

**4.**

**You must include at least one class level ActiveRecord scope method. 
**

**Your scope method must be chainable, meaning that you must use ActiveRecord Query methods within it (such as .where and .order) rather than native ruby methods (such as #find_all or #sort).**

Scoping adds a class method for retrieving and querying objects. The method is intended to return an ActiveRecord::Relation object, which is composable with other scopes. If it returns nil or false, an all scope is returned instead.

Here we first add

```
class Pizza < ApplicationRecord
    scope :search_by_address, -> (search_address){where("delivery_address = ?", search_address)}

```

then

```
class PizzasController < ApplicationController
    
  def index
      if params[:delivery_address]
      @pizzas = Pizza.search_by_address(params[:delivery_address])

```

and to Pizza index.html.erb a search form

```
<%= form_tag("/pizzas", method: "get") do %>
<%= label_tag(:delivery_address, "Search pizza by address") %>
  <%= text_field_tag(:delivery_address) %>
  <%= submit_tag("Search") %>
<% end %>

```

that returns all pizzas found in database WHERE Pizza delivery address attribute matches text field input that is user creates and adds to params for delivery address.

[https://api.rubyonrails.org/classes/ActiveRecord/Scoping/Named/ClassMethods.html#method-i-scope](http://)

[https://guides.rubyonrails.org/active_record_querying.html](http://)

**5.**

**You must include and make use of a nested resource with the appropriate RESTful URLs.**

Config/routes.rb :

```
  resources :users do
    resources :pizzas, only: [:new, :create, :index]
  end

```

Pizzas/index.html.erb that represents INDEX nested route:

```
@pizzas.each do |pizza|  
link_to pizza.user.name, user_pizzas_path(pizza.user) 
end 

```

Users/show.html.erb that represents a NEW nested route:

```
<p><%= link_to '--> Create a new Pizza <--', new_user_pizza_path(@user.id), class: "a-primary3" %></p>
```

[https://guides.rubyonrails.org/routing.html](http://)

**6.**

**Your application must provide standard user authentication, including signup, login, logout, and passwords.
**

**To pass this requirement we need to configure all aspects of the App. 
**

Here are some signup steps:

Config/routes.rb

```
  get '/signup' => 'users#new'
  post '/signup' => 'users#create'

```

Models/users.rb and database User table needs to be created along with users_controller :

```
class UsersController < ApplicationController
  
      def new
          @user = User.new
      end
      
      def create
        @user = User.new(user_params)
        if @user.save
          session[:user_id] = @user.id
          redirect_to user_path(@user)
        else
          render :new
        end
      end

      private

      def user_params
        params.require(:user).permit(:name, :email, :password)
      end
```

Lastly we add form to create params for controller Views/users/new.html.erb:

```
<%= form_for @user do |f| %>
  <%= f.label :name , class: "a-primary"%>
  <%= f.text_field :name %><br />
  </br>
  <%= f.label :email , class: "a-primary"%>
  <%= f.text_field :email %><br />
  </br>
  <%= f.label :password , class: "a-primary"%>
  <%= f.password_field :password  %><br />
  </br>
  <%= f.submit %>
<% end %>
</div>

```

**7.**

**Your authentication system must also allow login from some other service. Facebook, Twitter, Foursquare, Github, etc...
**

For this last requirement I won't go into details but will just recommend this blog that phenomenally explains this step by step.

[https://medium.com/@rachel.hawa/google-authentication-strategy-for-rails-5-application-cd37947d2b1b](http://)

