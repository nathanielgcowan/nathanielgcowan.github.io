---
layout: post
title:      "Sinatra Project"
date:       2020-10-05 02:48:36 +0000
permalink:  sinatra_project
---




After pondering different ideas I decided to make a project about cars. One of my favorite subjects.

The ojbective is to provide a CRUD, MVC app using Sinatra for fans of cars.





Installation instructions was built with the corneal gem, which provides this directory structure.

├── config.ru
├── Gemfile
├── Gemfile.lock
├── Rakefile
├── README
├── app
│   ├── controllers
│   │   └── application_controller.rb
|   |   └──create_posts_controller.rb
|   |   └──drivers_controller.rb
|   |   └──posts_controller.rb
|   |   └──sessions_controller.rb
|   |   └──users_controller.rb
│   ├── models
|   |   └──car.rb
|   |   └──create_post.rb
|   |   └──driver.rb
|   |   └──session.rb
|   |   └──user.rb
|   |   └──vehicle.rb
│   └── views
│       ├── cars
│       ├── create_posts
│       ├── drivers
│       ├── posts
│       ├── sessions
│       ├── users
│       ├── vehicles
│       ├── layout.erb
│       └── welcome.erb
├── config
│   ├── initializers
│   └── environment.rb
├── db
│   └── migrate
│   └── schema.rb
├── lib
│   └── .gitkeep
└── public
|   ├── images
|   ├── javascripts
|   └── stylesheets
|       └── main.css
└── spec
    ├── application_controller_spec.rb
    └── spec_helper.rb

Installation
gem install cars-mode

Contributors Guide


ApplicationController
My application controoler is where I bring users to initially

I direct their sessions whether that are or not logged in with helper methods.



def logged_in?
    !!current_driver
end

def current_driver
    @current_driver ||= Driver.find_by(:email => session[:email]) if session[:email]
end

def login(email, password)
    driver = Driver.find_by(:email => email)
    if driver && driver.authenticate(password)
    session[:email] = driver.email
    else
    redirect '/login'
    end
end

def logout!
    session.clear
end


my CreatePost controller is my CRUD for the customers to make their car forms. my helper methods make sure the right person is making changes.

class CreatePostsController < ApplicationController
  @car = CreatePost.new
  @car.model =params[:model]
  @car.year = params[:year]
  @car.driver_id = params[:driver_id]

  get '/' do
    redirect "/create_posts"
  end

  GET: /index
  get "/create_posts" do
    "A list of publically available posts"
    @cars = CreatePost.all
    erb :"/create_posts/index.html"
  end

  new
  get "/create_posts/new" do
      erb :"/create_posts/new.html"
  end

  post "/create_posts" do
      @car = CreatePost.create(:model => params[:model], :year => params[:year])
      redirect "/create_posts/#{@car.id}"
  end

  show
  get "/create_posts/:id" do
    @car = CreatePost.find_by_id(params[:id])
    erb :"/create_posts/show.html"
  end

  edit load edit form
  get "/create_posts/:id/edit" do
    if !logged_in?
      redirect "/login"
    else
      @car = CreatePost.find(params[:id])
      erb :"/create_posts/edit.html"
    end
  end

  patch "/create_posts/:id" do #edit action
    @car = CreatePost.find_by_id(params[:id])
    @car.model = params[:model]
    @car.year = params[:year]
    @car.save
    redirect to "/create_posts/#{@car.id}"
  end

  destroy
  delete "/create_posts/:id" do #delete action
    if !logged_in?
      redirect "/login"
    else
      @car = CreatePost.find_by_id(params[:id])
      @car.model = params[:model]
      @car.year = params[:year]
      @car.id = params[:id]
      @car.delete
      redirect to "/create_posts"
    end
  end
end


My drivers_controller is my users_controller. the most notable part about the controller is establishing new users.

  POST: /drivers
  post "/drivers" do
    @driver = Driver.new
    @driver.email =params[:email]
    @driver.password = params[:password]
    if @driver.save
      redirect '/login'
    else
      erb :"drivers/new.html"
    end
  end


My create_post method belongs to my driver method.

My driver.rb has_many create_post and secure passwords.

My views>create_posts hold the majoritory of my forms to be edited by the user.

My views>sessions. hold the majority of my user authentication files


My confir.ru file is allows for edits because I have 

use Rack::MethodOverride

before any controller

and I ...

run ApplicationController

last.

download repo from GitHub
https://github.com/nathanielgcowan/cars-mode

cd cars-mode
bundle install

code . [Enter]

you should now be 

Here is a link to the license of my code
https://github.com/nathanielgcowan/cars-mode/blob/main/LICENSE

