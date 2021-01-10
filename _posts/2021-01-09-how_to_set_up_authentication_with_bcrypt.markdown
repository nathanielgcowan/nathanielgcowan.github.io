---
layout: post
title:      "How to Set Up Authentication with BCrypt"
date:       2021-01-09 18:33:00 -0500
permalink:  how_to_set_up_authentication_with_bcrypt
---


Many people despite warnings will reuse passwords and usernames for different accounts. This means that if your data is breached, someone’s passwords for their bank account could potentially be exposed. Therefore, we as developers need to go the extra mile to prevent this from happening.

This is why we use BCrypt. An open-source gem to hash passwords to keep that plain text out of view. Once salted and hashed, it’s very difficult to decode another’s password. It taks hackers a long time to decode anything because ...

**BCrypt is Rather Slow**

The one drawback of such a large gem is that it slows down your application. It's designed this way to make it harder for hackers to decode your database, but a slower log in speed is a good compromise for a secure database.

**First, Let’s Talk About Hashes**

Hashes are numbers computed by feeding to a hash function. Hash functions have the property that they will always produce the same number given the same input.

For example, you can save a password by saying ```User.password = *new_password*.```

BCrypt hashes similar strings to different values. Therefore making it rather difficult to decode and hack an account. BCrypt also adds increased security by salting hashes.

**What Is a Salted Hash You Ask?**

Well, a salt is a random string prepended to the password before hashing it. The string is juxtaposed to the passwords, stored in plain text. This makes the act of hacking rather difficult to perform. Even if a hacker can crack the bcrypt code, most likely they won’t know the string you salted on all of your passwords.

**But How Does BCrypt Use a Salted Hash**

BCrypt will store a salted hash in a column called ```password_digest```. Which, pretty much means a salted hash will not be decoded.

The following is how to implement BCrypt

1. Add the ‘bcrypt’ gem to your Gemfile.
2. Add password_digest to a column in your migration

```
Class CreateUsers < ActiveRecord::Migration[5.1]
	Def up
		Create_table :users do |t|
			T.string :username
			T.string :password_digest
		End
	End
	
end
```
Run this migration using rake db:migrate. Preview your work by running shotgun and navigating to localhost:9393 in your browser. Awesome job!
Comfirm the password and the password_confirmation are a match

**ActiveRecord’s has_secure_password**

The has_secure_password macro when called creates a method for you in conjunction with bcrypt and gives us all of those abilities in a secure way that doens’t store the plain text password in the database.

Below is how to use it.
```
Class Use < ActiveRecord::Base
	Has_secure_password
End
```
This will tell Ruby to add the ```authenticate``` method to our program that will run in the background of our program.(This is an example of metaprogramming)

```
Post “/login” do
	User = User.find_by(:username => params[:username])

	If user && user.authenticate(params[:password])
		Session[:user_id] = user.id
		Redirect “/success”
	Else
		Redirect “/failure”
	End
end
```

Tay-dah!! You know know how to set up authentication with BCrypt


**But How Does the Authenticate Method Work Exactly?**

The authenticate method takes a string, turns it into a salted hash, compares the hash to the one in the database, and if they match will return the User.

**Takeway**

Overall, BCrypt is a really good choice for passwords.Even if a hacker gets a hold of your database they are going to struggle with turning a hash back into a string.
Even if they ran a list of common words from Webster’s dictionary, it would take them a considerable amount of time to decode anything at all.

To set up this authentication method with bcrypt, make sure to implement the gem within your Gemfile, add the password_digest column to your migration, and apply ActiveRecord’s has_secure_password,to your model










