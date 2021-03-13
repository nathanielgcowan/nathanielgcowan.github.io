---
layout: post
title:      "The Different Methods That ActiveRecord Provides Us"
date:       2021-03-12 09:00:24 -0500
permalink:  the_different_methods_that_activerecord_provides_us
---


The Active Record Query Interface is a powerful tool in Ruby. Instead of wasting time using long SQL commands, Rails users can take advantage of this gem to carry out the same operations. In this article ,we are going to take about three fundamental methods Active Record provides us (find, create, update) and go over why they are important tools for you to have.

The find method in Rails is commonly used in the controller. It’s widely used for returning record values by id.

The find method can be used in many different ways. You can control what is returned, and the order you receive it.

This can be done in four ways: find by id, find all, find first, and find last.

## Find by id
	The find method can be called with a single id or an array of ids. Which you can return an array of values and find the return everything with those id values.
find(id)
```
2.6.1 :007 > User.find(1)
  User Load (0.8ms)  SELECT "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
 => #<User id: 1, email: "nathaniel@flatiron.com", created_at: "2021-03-04 04:32:36", updated_at: "2021-03-04 04:32:36", provider: "", uid: "">
```


### Find All
Find used with :all returns all matching values. This can also be abbreviated to just all.
find(:all)
```
2.6.1 :008 > User.all
  User Load (1.8ms)  SELECT "users".* FROM "users" LIMIT ?  [["LIMIT", 11]]
 => #<ActiveRecord::Relation [#<User id: 1, email: "nathaniel@flatiron.com", created_at: "2021-03-04 04:32:36", updated_at: "2021-03-04 04:32:36", provider: "", uid: "">, #<User id: 2, email: "nathaniel.g.cowan@gmail.com", created_at: "2021-03-04 05:08:25", updated_at: "2021-03-04 05:08:25", provider: "google_oauth2", uid: "117918496875385073066">, #<User id: 3, email: "nathaniel.cowanmedia@gmail.com", created_at: "2021-03-06 04:43:58", updated_at: "2021-03-06 04:43:58", provider: "", uid: "">, #<User id: 4, email: "dustin@flatiron.com", created_at: "2021-03-10 20:36:04", updated_at: "2021-03-10 20:36:04", provider: "", uid: "">]>
```


### Find First
Find with :first returns the first match. This can be abbreviated to just first.
Find with :first returns the first match.
```
2.6.1 :009 > User.first
  User Load (0.8ms)  SELECT "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ?  [["LIMIT", 1]]
 => #<User id: 1, email: "nathaniel@flatiron.com", created_at: "2021-03-04 04:32:36", updated_at: "2021-03-04 04:32:36", provider: "", uid: "">
```


### Find Last
Find with :last returns the last match. This can be abbreviated to just last.
```
2.6.1 :010 > User.last
  User Load (1.1ms)  SELECT "users".* FROM "users" ORDER BY "users"."id" DESC LIMIT ?  [["LIMIT", 1]]
 => #<User id: 4, email: "dustin@flatiron.com", created_at: "2021-03-10 20:36:04", updated_at: "2021-03-10 20:36:04", provider: "", uid: "">
```


## Create
Active Record objects can be created from a hash,a block, or have their attributes manually set after creation. Create is special in that it will return an object and save it to the database, if validations pass. The resulting object is returned whether the object was saved successfully to the database or not.

The attributes parameter can be either a Hash or an Array of Hashes. These Hashes describe the attributes on the objects that are to be created.

Examples
```
# Create a single new object.
User.create(email: "email@email.com")

# Create an Array of new objects
User.create([{ email:'one@email.com' }, { email: 'two@email.com'}])

# Create a single object and pass it into a block to set other attributes.
User.create(email: 'user@email.com') do |user|
	User.is_admin = false
End

# Creating an Array of new objects using a block, where the block is executed for each object:
User.create([{ email: 'one@email.com'}, {email: 'two@email.com' }]) do |user|
	User.is_admin = false
End
```


## Update
This command is used to update the object(or multiple objects) and save them to the database, if validations pass. The resulting object is returned whether the object was saved successfully to the database or not.

In order to update you will need both the id and the attributes.
For example:
```
# Updates one record
 User.update( 1, email: "austin")

# Updates multiple records
User = { 1 => "email" => "one" }, 2 => { "email" => "two"}}
User.update(user.keys, user.values)

# Updates multiple records from the result of a relation
User = User.where(email: "nathaniel@flatiron.com")
user.update(email: "nathaniel@flatironschool.com")
```

Overall, ActiveRecord is a very powerful gem that can help us easily find data in our database. The find method is very flexible in the data it returns you, the create method returns then saves an object to your database, and update is able to patch existing objects. It would behoove every Ruby programmer to keep these methods in their back pocket because they make your code easier to both read and write.

