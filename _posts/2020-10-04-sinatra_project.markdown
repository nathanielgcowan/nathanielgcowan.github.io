---
layout: post
title:      "Sinatra Project"
date:       2020-10-04 22:48:37 -0400
permalink:  sinatra_project
---


My project is a MVC Sinatra application that utilizes ActiveRecord. There are two main models to my project: user and trucks. Their relationship consist of a user having many trucks, while a truck belongs to a user.

Within the user account, anyone can sign up, sign in and log  out of their accounts securely due to the Bcrypt gem.

Their account is validated by email and password.

Once logged on, users are able to create, edit, and delete any off their own trucks.
To prevent people from editing someone else’s truck. I match the current session to the user_id associated with each truck. Therefore only the user that originally made the truck can make any changes to it.

To prevent any bad data from populating in the system users must make the pattern for email within signup and not leave a field blank when making or editing a truck.

I started with the corneal gem.

```corneal new eight```

Then I ran bundle install from my app’s directory

```cd eight```

```bundle install```

Then I started a server with shotgun.

``` shotgun ```

I then generated my scaffolds.

```corneal scaffold user email:string password_digest:string```

```corneal scaffold truck model:string make:string user_id:integer```

```corneal scaffold session```

The resulting structure looked like this:

```
- app
	- controllers
		-application_controller.rb
		-sessions_controller.rb
		-trucks_controller.rb
		-users_controller.rb
	- models
		-session.rb
		-truck.rb
		-user.rb
	- views
		-sessions
			-edit.html.erb
			-index.html.erb
			-new.html.erb
			-show.html.erb
		-trucks
			-edit.html.erb
			-index.html.erb
			-new.html.erb
			-show.html.erb
		-users
			-edit.html.erb
			-index.html.erb
			-new.html.erb
			-show.html.erb 
```

So I ran db:migrate and bundle install one more time and off I went to create the rest of my app.

***Gemfile***
```
I require the following gems
Gem ‘sinatra
Gem ‘activerecord’
Gem ‘sinatra-activerecord’
Gem ‘rake’
Gem ‘require-all’
Gem ‘sqlite3’
Gem ‘thin’
Gem ‘shotgun’
Gem ‘pry’
Gem ‘bcrypt’
Gem ‘tux’
```

Then I ran bundle install and started with my application_controller.rb file.

**Application_controller.rb **

In this file I enabled sessions and created my helper methods for user authentication.
This is my following file.
```
require './config/environment'

class ApplicationController < Sinatra::Base

  configure do
    set :public_folder, 'public'
    set :views, 'app/views'
    enable :sessions
    set :session_secret, "carcollection"
  end

  get '/' do 
    redirect "/trucks"
  end

  helpers do

    def logged_in?
      !!current_user
    end

    def current_user
      @current_user ||= User.find_by(:email => session[:email]) if session[:email]
    end

    def login(email, password)
      user = User.find_by(:email => email)
      if user && user.authenticate(password)
        session[:email] = user.email
        session[:id] = user.id
        
      else
        redirect '/login'
      end
    end

    def logout!
      session.clear
    end

  end
end```



**Sessions_controller.rb 
**
```
class SessionsController < ApplicationController

  get "/login" do
    erb :"/users/index.html"
  end

  # POST: /sessions
  post "/sessions" do
    login(params[:email], params[:password])
    redirect '/trucks'
  end

  get '/logout' do
    logout!
    redirect '/trucks'
  end
end
```



**Trucks_controller.rb
**

```
class TrucksController < ApplicationController

  get '/trucks' do 
    @trucks = Truck.all 
    erb :"trucks/index.html"
  end

  get '/trucks/new' do
    if current_user 
      erb :"trucks/new.html" 
    else 
      redirect "/login"
    end
  end

  post '/trucks' do 
    #{"model"=>"model of truck", "make"=>"make of truck"}
    @truck = Truck.new
    @truck.model = params[:model]
    @truck.make = params[:make]
    # this is already created
    @truck.user_id = session[:id]
    if @truck.save
      redirect "/trucks/#{@truck.id}"
    else
      erb :"trucks/new.html"
    end
  end 

  get '/trucks/:id' do 
    @truck = Truck.find_by_id(params[:id])
    erb :"trucks/show.html"
  end

  get '/trucks/:id/edit' do 
    @truck = Truck.find(params[:id])
    @truck.model = params[:model]
    @truck.make = params[:make]

    if session[:id] == @truck.user_id
      erb :"trucks/edit.html"
    else
      redirect "/trucks"
    end
  end

  patch '/trucks/:id' do
    @truck = Truck.find(params[:id])
    @truck.model = params[:model]
    @truck.make = params[:make]
    @truck.user_id = session[:id]
    @truck.save 

    redirect "/trucks/#{@truck.id}"
  end

  delete '/trucks/:id' do
    @truck = Truck.find(params[:id])
    @truck.destroy

    redirect '/trucks'
  end
end
```



# Models
**User.rb** 
```
This file determines this encrypted password, validates for a valid information, and establish the has many relationship with trucks
class User < ActiveRecord::Base
    has_secure_password
    validates :email, :presence => true
    validates :password_digest, :presence => true
    has_many :trucks
end
```


**Truck.rb**
```
This file validates information and establishes the relationship with users.
class Truck < ActiveRecord::Base
    belongs_to :user
    validates :model, :presence => true
    validates :make, :presence => true
end
```


[Here is my code on GitHub](https://github.com/nathanielgcowan/trucks_draft). Thank you for reading

