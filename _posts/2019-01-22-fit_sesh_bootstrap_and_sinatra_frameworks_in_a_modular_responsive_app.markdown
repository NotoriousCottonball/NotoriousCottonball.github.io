---
layout: post
title:      "Fit Sesh: Bootstrap and Sinatra Frameworks in a Modular, Responsive App "
date:       2019-01-22 08:39:53 +0000
permalink:  fit_sesh_bootstrap_and_sinatra_frameworks_in_a_modular_responsive_app
---


<iframe width="560" height="315" src="https://www.youtube.com/embed/kaTy_E-92eE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

My second Flatiron School project involved creating a Sinatra Web Application with a Modular Structure, Database Persistence, and Responsive Web Pages. The project required a strong grasp of the Sinatra DSL, Object-Relational Mapping via ActiveRecord, and the Bootstrap Front-End Framework. The project approached building a Content Management System(CMS) through a Model, View, Controller(MVC) Development Paradigm and  RESTful Routing.

---------

The **fit_sesh** code repository is on [github](https://github.com/NotoriousCottonball/fit_sesh).

---------

## I. The Inspiration for Fit Sesh

I worked part-time as a personal trainer through law school, so fitness applications hold a special interest for me. After learning Active Record, I started thinking about the parallels between E-commerce site models and categories in Fitness Tracking. The basic objects in online shopping are the User, the Cart, the Item, and the Line-Item. User's have Carts, through which they have Line-Items, while Items have many Line-Items. Carts and Items therefore have a many-to-many relationship described through Line-Items. 

In a similar vein, Fitness Applications feature Users, Workouts, Exercise, and Exercise Entries. Users have Workouts, through which they have Exercise-Entries, while Exercises have many Exercise-Entries. Workouts and Exercises therefore have a many-to-many relationship described through Exercise-Entries. 

These relationships allow for some really useful association of Data if User's Log, Track, and Share their Workout Data. Although expanding the application to fully leverage these relationships was beyond the scope of the project, I set out to demonstrate the concept by giving user's access to 1.) Exercise Ratings (sourced from Averages of Exercise_Instance ratings), and 2.) Sorted Lists of User's who Frequently Perform or Highly Rate every Exercise. 

In addition to allowing User's to Log/Store their Workout Data, Fit Sesh let's them decide to either Share/Keep Private each Workout. The User Index and Exercise Index are therefore able to provide a source of recommendations and relevant user data to inspire/inform workouts. 


## II. Project Directory Structure

```
├── config
│   └── environment.rb
├── app 
│   ├── controllers
│       └── application_controller.rb
│   ├── models
│   └── views
├── db  
├── config.ru 
├── Rakefile 
├── Gemfile

```

*config.ru* – Runs the Application. Mounts Controllers and declares use of Rack::MethodOverride for Patch/Delete Routes. 


*config/environment.rb* –Loads dependencies, such as gems, databases, and the MVC files with the following code:

```
ENV['RACK_ENV'] ||= "development"

require 'bundler/setup'
Bundler.require(:default, ENV['RACK_ENV'].to_sym)

configure ENV['RACK_ENV'].to_sym do
  set :database, "sqlite3:db/#{ENV['RACK_ENV']}.sqlite"
end

require_all 'app'
```

*app* – Holds the MVC folders of “models”, “views”, and “controllers”. The Main Directory and Meat of the Application. 

*gems* – I included 12 gems in my Gemfile: 

```
gem 'sinatra'
gem 'activerecord', '~> 5.1', '>= 5.1.5', :require => 'active_record'
gem 'sinatra-activerecord', :require => 'sinatra/activerecord'
gem 'rake'
gem 'require_all'
gem 'sqlite3'
gem 'thin'
gem 'shotgun'
gem 'pry'
gem 'bcrypt'
gem 'tux'
gem 'sinatra-flash'
```

*Rakefile* – I challenged myself a bit with Rake Tasks, writing a task to generate a random 64 hex string to use as a session_secret in the event that ENV['SESSION_SECRET'] isn't set. The task runs a shell script,  setting a user-level bundle config setting to the result of a SecureRandom generated string:

```
ENV['RACK_ENV'] ||= "development"
require_relative './config/environment'
require 'sinatra/activerecord/rake'

desc 'Sets session secret'
task :session_secret do
  require 'securerandom'
  secure_random = SecureRandom.hex(64)
  `bundle config session_secret #{secure_random}`
  puts "SESSION_SECRET: #{`bundle config session_secret`.slice(142..-1)}"
end

desc 'Shows current environment'
task :show_environment do
  puts "RACK_ENV: #{ENV['RACK_ENV']}\nSESSION_SECRET: #{`bundle config session_secret`.slice(142..-1)}"
end

```

*app/controllers/application_controller.rb*- The Configuration Settings in the Main Application Controller enable sessions and set the session_secret to the result of the above Rake Task if unable to fetch the indicated Environment variable:

```
require './config/environment'
class ApplicationController < Sinatra::Base

  configure do
    set :public_folder, 'public'
    set :views, 'app/views'
    enable :sessions
    set :session_secret, ENV.fetch('SESSION_SECRET'){`bundle config session_secret`.slice(142..-1)}
    register Sinatra::Flash
  end
	
	[...]
```

## III. M.V.C / Modularity / Separation of Concerns

The *app* directory structures the application according to the Model, View, Controller paradigm. Sinatra already separates Views from Routes(Controllers). Basically, I added an "M" by using ActiveRecord as a database framework to generate Model functionality,  and then distributing appropriate logic between the Models and Controllers. 

An M.V.C app feature strict separation of concerns, with the Views fetching data from the Controllers before they distinctly generate HTML. Data Logic stays in the Models (Object Classes, Cocerns, and Database), Mapping Logic in the Controllers, and Display Logic in the Views(handled via Embedded Ruby(ERB)). 

The application was built around the Distinct Categories of Object(Users, Workouts, Exercises, Exercise_Instances) and their context/scope within the application. 

In order to  categorize these concerns at the controller level, ***Fit Sesh***  extends the `Sinatra::Base` class to the main *ApplicationController*, and then uses four additional controllers, inheriting from the main controller, each handling requests in their own context and scope.

*ApplicationController* – Configuration settings (views, public folder, sessions, session_secret, register `Sinatra::Flash`),  the Index Route, and Helper Methods(logged_in?/current_user/etc...)

*UsersController* – Sign-up Routes, Log-in Routes , Log-out Route, Show User Routes, User Index Route. 

*WorkoutsController* – Show Workout Route, New Workout Routes, Edit Workout Routes, Delete Workout Route. 

*ExercisesController* – Show Exercise Route, Exercise Index Route, New Exercise Routes, Delete Exercise Route.

*ExerciseInstancesController* – New ExerciseInstance Routes,  Delete ExerciseInstance Route.


The controllers displays RESTful Routing, and where applicable, uses the following routes:

* GET Request: 

-Render Main Index Page,  User Index, User Profile, Workout Page, and Exercise Page.
-Render Login Page, Signup Page, New Workout Form, Edit Workout Form, New Exercise Form, and New ExerciseInstance Page. 
-Logout(POST could be used as well)

* POST Request:

-Process Login Form, Signup Form, New Workout Form, New Exercise Form, and New ExerciseInstance Form.

* PATCH Request:

-Process Edit Workout Form.

* DELETE Request:

-Destroy Workout, Exercise, and ExerciseInstance objects.


## IV. Object Relational Mapping(ORM)

***Fit Sesh*** uses the ActiveRecord Database Framework with sqlite as a database adapter. The database schema determines most of the Model Logic via the Table Structure:

```
ActiveRecord::Schema.define(version: 2019_01_05_124758) do

  create_table "exercise_instances", force: :cascade do |t|
    t.integer "exercise_id"
    t.integer "workout_id"
    t.integer "weight_lbs"
    t.integer "sets"
    t.integer "reps"
    t.integer "exercise_rating"
    t.string "notes"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "exercises", force: :cascade do |t|
    t.string "name"
    t.string "description"
  end

  create_table "users", force: :cascade do |t|
    t.string "username"
    t.string "email"
    t.string "password_digest"
  end

  create_table "workouts", force: :cascade do |t|
    t.integer "duration_minutes"
    t.integer "user_id"
    t.boolean "share_status", default: false, null: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

end
```

Together with ActiveRecord Associations and Validations in the Models Directory:

1. User Model

```
class User < ActiveRecord::Base
  has_secure_password

  has_many :workouts
  has_many :exercises, :through => :workouts
  has_many :exercise_instances, :through => :workouts

  validates :username, presence: true
  validates :username, uniqueness: true
  validates :email, presence: true
  validates :email, uniqueness: true
  validates_format_of :email, :with => /\A[^@\s]+@([^@\s]+\.)+[^@\s]+\z/
  validates :password, length: {minimum: 6}, allow_nil: true #loses the cached value for password on reload from database

  extend Slugifiable::ClassMethods
  include Slugifiable::InstanceMethods
end
```

2. Workout Model

```
class Workout < ActiveRecord::Base

  belongs_to :user
  has_many :exercise_instances
  has_many :exercises, :through => :exercise_instances
  
  validates :duration_minutes, presence: true
  validates :user_id, presence: true
  validates :share_status, inclusion: {in: [true, false]}

  extend Slugifiable::ClassMethods
  include Slugifiable::InstanceMethods
	end
```

3. Exercise Model

```
class Exercise < ActiveRecord::Base

  has_many :exercise_instances
  has_many :workouts, :through => :exercise_instances
  has_many :users, :through => :workouts

  validates :name, presence: true
  validates :name, uniqueness: true
  validates :description, presence:true

  extend Slugifiable::ClassMethods
  include Slugifiable::InstanceMethods

end
```

4. ExerciseInstance Model 

```
class ExerciseInstance < ActiveRecord::Base

  belongs_to :workout
  belongs_to :exercise
  delegate :user, :to => :workout

  validates :workout_id, presence: true
  validates :exercise_id, presence: true
  validates :sets, presence: true
  validates :reps, presence: true
  validates :exercise_rating, inclusion: {in: [1, 2, 3, 4, 5]}

  extend Slugifiable::ClassMethods
  include Slugifiable::InstanceMethods
end
```

These associations, with ExerciseInstances acting as a join table between Workouts and Exercises, allows for precise queries for useful grouping of fitness data. The following User Profile query, for example, sorts a User's Top 3 Exercises by Average ExerciseInstance Rating:

```
@user.exercise_instances.group(:exercise).average(:exercise_rating).sort_by {|k,v| v}.reverse.take(3)
```

## V. Bootstrap and Views

Finally, ***Fit Sesh*** uses the  Bootstrap Front-End Framework to Display a Stylish and Responsive Website. 

*app/views/layout.erb*– Sinatra's layout feature provides a standardized page structure for common elements like the Navbar and Flash Messages. The Layout Template displays a navbar if the user is logged in, displays Flash Messages if passed by Sinatra::Flash, and then yields to designated View Templates:

```
<div class="container">
    <div class="d-flex justify-content-center">
    <% if logged_in? %>
      <nav class="rounded-circle border-custom navbar navbar-dark navbar-expand-lg py-5 px-5" style="background-color: rgba(1, 5, 54);">
        <a class="navbar-brand" href="/">
          <img src="/images/fit_sesh_favicon.png" class="d-inline-block border border-primary mb-2 mr-2" width="35" height="35" alt="">
          <span class="h2">Fit Sesh</span>
        </a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon" ></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item">
              <a class="nav-link" href="/workouts/new">
                <span class="h3">Log Workout</span>
              </a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="/">
                <span class="h3">| &nbsp;&nbsp; Home &nbsp;&nbsp;|</span>
              </a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="/logout">
                <span class="h3">Logout</span>
              </a>
            </li>
          </ul>
        </div>
      </nav>

    <% end %>
    </div>

    <div class=" mt-3 container text-center">
      <% if flash[:message] %>
        <h3 style="color: dodgerblue;"><%= flash[:message] %><h3>
      <% end %>
      <% if flash[:error] %>
        <h3 style="color: crimson;"><%= flash[:error] %></h3>
      <% end %>
    </div>

    <%= yield %>
    <div class="navbar navbar-default navbar-fixed-bottom justify-content-end">
       <p class="mr-3 font-weight-light text-dark">Fit Sesh &copy; 2019</p>
    </div>
  </div>
```


*app/views/layout.erb*– The Layout Template makes the call out to the Bootstrap CDN, and then links to the Supplemental Stylesheet in the Public Folder:

```
 <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
    <link rel="stylesheet" href="/stylesheets/stylesheet_supplement.css">
```

While Bootstrap CSS provides the framework for responsive design elements, the local stylesheet modifies style elements in a handful of places to provide a custom look. 

For example, the Jumbotron on the Index, Login, and Signup pages results from this addition:

```
jumbotron-transparent {
  margin:15px 30px 15px 30px;
  border-radius:10px;
  background: linear-gradient(rgba(0, 0, 250, 0.25), rgba(125, 250, 250, 0.45)), url(/images/fit_sesh_jumbotron.jpg);
  background-repeat: no-repeat;
  background-attachment: fixed;
  color: white;
}
```

Overall, the Sinatra Portfolio Project provided an immersive experience in bringing together Front-End and Back-End Development. As design and structural complexity grows, the importance of programming paradigms like M.V.C and Modular design become increasingly apparent. 

---------

## Links


[The project repository on github](https://github.com/NotoriousCottonball/fit_sesh)
