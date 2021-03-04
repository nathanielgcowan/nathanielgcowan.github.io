---
layout: post
title:      "Rails Project"
date:       2021-03-04 19:04:21 +0000
permalink:  rails_project
---



My project is a Ruby on Rails application that manages related data through complex forms and RESTful routes. My project has four models: user, car, part, and vehicle, with various has_many, belong_to, and many to many relationships.
My app uses Devise and OmniAuth for sign up, login, and logout.

Once logged on, users are able to create, edit, and delete any of their own cars and parts. To prevent people from editing someone else’s car or part. I match the current session to the user_id associated with each car and part. Therefore only the user that originally made the car or part can make any changes to it.

I made my app
`Rails new nineteen  # local repo's name`


Then I ran bundle install from my app’s directory
`cd nineteen`


`bundle install`


Then I started a server.
`Rails s`


Then I wrote out my database. This is the resulting schema.
```
# This file is auto-generated from the current state of the database. Instead
# of editing this file, please use the migrations feature of Active Record to
# incrementally modify your database, and then regenerate this schema definition.
#
# This file is the source Rails uses to define your schema when running `rails
# db:schema:load`. When creating a new database, `rails db:schema:load` tends to
# be faster and is potentially less error prone than running all of your
# migrations from scratch. Old migrations may fail to apply correctly if those
# migrations use external dependencies or application code.
#
# It's strongly recommended that you check this file into your version control system.

ActiveRecord::Schema.define(version: 2021_03_04_062105) do

  create_table "cars", force: :cascade do |t|
    t.string "make"
    t.string "model"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
    t.integer "user_id"
    t.integer "rating"
    t.integer "year"
    t.integer "vehicle_id"
  end

  create_table "parts", force: :cascade do |t|
    t.string "name"
    t.integer "user_id"
    t.integer "car_id"
    t.integer "rating"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

  create_table "sessions", force: :cascade do |t|
    t.string "session_id", null: false
    t.text "data"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
    t.index ["session_id"], name: "index_sessions_on_session_id", unique: true
    t.index ["updated_at"], name: "index_sessions_on_updated_at"
  end

  create_table "users", force: :cascade do |t|
    t.string "email", default: "", null: false
    t.string "encrypted_password", default: "", null: false
    t.string "reset_password_token"
    t.datetime "reset_password_sent_at"
    t.datetime "remember_created_at"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
    t.string "provider", limit: 50, default: "", null: false
    t.string "uid", limit: 500, default: "", null: false
    t.index ["email"], name: "index_users_on_email", unique: true
    t.index ["reset_password_token"], name: "index_users_on_reset_password_token", unique: true
  end

  create_table "vehicles", force: :cascade do |t|
    t.string "name"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end
end
```

So I ran rake db:migrate and bundle install one more time and off I went to create the rest of my app!
These are the gems I added to my Gemfile

```
gem 'devise', '~> 4.7', '>= 4.7.3'
gem 'omniauth', '~> 1.0', '>= 1.0.0'
gem 'omniauth-google-oauth2'
gem 'activerecord-session_store'
gem 'pry'
```

**Application_controller.rb**
In this file I created one of my helper methods.
```
class ApplicationController < ActionController::Base
    helper_method :logged_in?
    
    def logged_in?
        !!current_user
    end
end
```

 
**Cars_Controller.rb**
This is where I wrote out the CRUD for the cars in my application.
```
class CarsController < ApplicationController
    before_action :authenticate_user!, except: [:index, :show, :highest_rating]
 
    def index
        @cars = Car.all
        @new_cars = Car.new_car
        @old_cars = Car.old_car
        @trucks = Car.truck
        @cars_list = Car.car
        @vans = Car.van
    end
 
    def highest_rating
        @cars = Car.all
        @good_car = Car.good_car
        @good_new_cars = Car.good_new_cars
        @good_old_cars = Car.good_old_cars
        @best_cars = Car.best_cars
        @best_vans = Car.best_vans
        @best_trucks = Car.best_trucks
    end
 
    def new
        @car = Car.new
    end
 
    def create
        vehicle = Vehicle.find_or_create_by(name: params[:car][:vehicle_name])
        @car = current_user.cars.build(car_params)
        if @car.save
            redirect_to car_path(@car)
        else
            render :new
        end
    end
 
    def show
        @car = Car.find(params[:id])
        @part = @car.parts.build
    end
 
    def edit
        @car = Car.find(params[:id])
    end
 
    def update
        @car = Car.find(params[:id])
        @car.update(car_params)
        if @car.save
            redirect_to car_path(@car)
        else
            render :edit
        end
    end
 
    def destroy
        @car = Car.find(params[:id])
        @car.destroy
        flash[:notice] = "Car deleted."
        redirect_to root_path
    end
 
    def car_params
        params.require(:car).permit(:make,:model,:year, :vehicle_name, :rating, parts_attributes: [:id, :name])
    end
end
```

 
**Omniauth Controller
This is the file controller that sets up the flow for OAuth in user registration.
class OmniauthController < ApplicationController
 
    def google_oauth2
        @user = User.create_from_provider_data(request.env['omniauth.auth'])
        if @user.persisted?
            sign_in_and_redirect @user
        else
            flash[:error] = 'There was a problem signing you in through Google. Please register or try signing in later.'
            redirect_to new_user_registration_url
        end
    end
 
    def failure
        flash[:error] = 'There was a problem signing you in. Please register or try signing in later.'
        redirect_to new_user_registration_url
    end
 
 
end
 



**Parts Controller **
This is the controller for my child object(parts), which established the CRUD for and relates to the parent object.

```
class PartsController < ApplicationController
    before_action :authenticate_user!, except: [:index, :show]
 
    def index
        if params[:car_id]
            @parts = Car.find(params[:car_id]).parts
        else
            @parts = Part.all
        end
    end
 
    def show
        @part = Part.find(params[:id])
    end
 
    def new
        if params[:car_id] && !Car.exists?(params[:car_id])
            redirect_to cars_path, alert: "Car not found."
        else
            @part = Part.new(car_id: params[:car_id])
        end
    end
 
    def create
        @part = current_user.parts.build(part_params)
        if @part.save
            redirect_to car_part_path(@part, @part)
        else
            flash.alert = "Fill out a valid ."
            render :new
        end
    end
    def edit
        if params[:car_id]
            car = Car.find_by(id: params[:car_id])
            if car.nil?
                redirect_to cars_path, alert: "Car not found."
            else
                @part = car.parts.find_by(id: params[:id])
                redirect_to car_parts_path(car), alert: "Part not found." if @part.nil?
            end
        else
            @part = Part.find(params[:id])
        end
    end
 
    def update
        @part = Part.find(params[:id])
        @part.update(part_params)
        if @part.save
            redirect_to part_path
        else
            render :edit
        end
    end
 
    def destroy
        @part = Part.find(params[:id])
        @part.destroy
        flash[:notice] = "Part deleted."
        redirect_to root_path
    end
 
    def part_params
        params.require(:part).permit(:name, :car_id, :user_id, :rating)
    end
end
```
 




**Vehicle Controller**
In this controller I wanted to establish the Vehicle types that people can submit data to but you won’t find any more routes for this controller, as it has been taken down to prevent invalid data.
```
class  VehiclesController < ApplicationController
    before_action :authenticate_user!
    def index
        @vehicles = Vehicle.all
    end
 
    def new
        @vehicle = Vehicle.new
    end
 
    def create
        @vehicle = Vehicle.new(vehicle_params)
        if @vehicle.save
            redirect_to vehicles_path(@vehicle)
        else
            render :new
        end
    end
 
    def vehicle_params
        params.require(:vehicle).permit(:name)
    end
end
```
 



**Models**
Car.rb
```
class Car < ApplicationRecord

    # Has Many / Belongs To
    has_many :parts
    has_many :users, through: :parts
    belongs_to :user
    belongs_to :vehicle

    # Nested Attributes
    accepts_nested_attributes_for :parts, allow_destroy: true

    # Validations
    validates :make, length: {minimum: 1}
    validates :model, :presence => true
    validates :model, :presence => true
    validates :year, :presence => true
    validates :rating, :presence => true
    validates_inclusion_of :year, :in => 1900..2021
    validates_inclusion_of :rating, :in => 1..5


    # Scope Methods
    scope :good_car, ->{where("rating >= 4")}
    scope :good_new_cars, -> {new_car.where("rating >= 4")}
    scope :good_old_cars, -> {old_car.where("rating >= 4")}
    scope :best_cars, -> {car.where("rating >= 4")}
    scope :best_vans, -> {van.where("rating >= 4")}
    scope :best_trucks, -> {truck.where("rating >= 4")}
    scope :good_and_new, -> {new_car.where("rating >= 4")}

    # Class Methods
    def self.new_car
        where("year > 1996")
    end
    def self.old_car
        where("year < 1996 ")
    end
    def self.car
        where("vehicle_id == 1")
    end
    def self.van
        where("vehicle_id == 3")
    end
    def self.truck
        where("vehicle_id == 2")
    end

    # Display Vehicle Name
    def vehicle_name=(name)
        self.vehicle = Vehicle.find_or_create_by(name: name)
    end
        
    def vehicle_name
        self.vehicle ? self.vehicle.name : nil
    end

end
```

 
**Part.rb**
```
class Part < ApplicationRecord
    
    # Has Many / Belongs To
    belongs_to :car
    belongs_to :user

    # Validations
    validates :name, :presence => true
    validates :rating, presence: true, numericality: { only_integer: true }
    validates_inclusion_of :rating, :in => 1..5

end
```
 
**User.Rb**
```
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable, :trackable and :omniauthable
  devise :database_authenticatable, :registerable,
        :recoverable, :rememberable, :validatable,
        :omniauthable, omniauth_providers: [:google_oauth2]
  
  def self.create_from_provider_data(provider_data)
    where(provider: provider_data.provider, uid: provider_data.uid).first_or_create do |user|
      user.email = provider_data.info.email
      user.password = Devise.friendly_token[0, 20]
    end
  end

  # Has Many / Belongs To
  has_many :cars
  has_many :parted_cars, through: :parts
  has_many :parts
  has_many :vehicles, through: :cars

  # Validations
  validates :email, uniqueness: true
  validates :name, :login, :email, presence: true

end
```


**Config/Routes**
```
Rails.application.routes.draw do
  root 'cars#index'
  get 'cars/ratings', to: 'cars#highest_rating'
  
  devise_for :users, controllers: {omniauth_callbacks: 'omniauth'}
  resources :cars
  resources :cars, only: [:show] do
    resources :parts, only: [:show, :index, :new,:create, :destroy]
  end
  resources :parts
  # resources :vehicles
  # For details on the DSL available within this file, see https://guides.rubyonrails.org/routing.html
end
```
 
 
[Here is my code on GitHub.](https://github.com/nathanielgcowan/Rails_Cars_8) Thank you for reading


