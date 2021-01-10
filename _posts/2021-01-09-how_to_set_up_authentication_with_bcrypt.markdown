---
layout: post
title:      "How to Set Up Authentication with BCrypt"
date:       2021-01-09 18:33:00 -0500
permalink:  how_to_set_up_authentication_with_bcrypt
---


![](http://www.fromzerotocode.com/2015/12/28/gem-bcrypt-on-windows)

Many people despite warnings will reuse passwords for different accounts. This means that if your data is breached, someone’s passwords for their bank account could potentially be exposed. Therefore, we as developers need to go the extra mile to prevent this from happening.

This is why we use BCrypt. A gem to hash passwords to keep that plain text out of view. Once salted and hashed, it’s very difficult to decode another’s password.

**First, Let’s Talk About Hashes**

Hashes are a numbers computed by feeding to a hash function. Hash functions have the property that they will always produce the same number given the same input.

For example, you can save a password by saying 
	User.password = *new_password*.

BCrypt hashes similar strings to different values. Therefore making it rather difficult to decode and hack an account. BCrypt also adds increased security by salting hashes

**What is a salted hash you ask?**

Well, a salt is a random string prepended to the password before hashing it. The string is juxtaposed to the passwords, stored in plain text. This makes the act of hacking rather difficult to perform. Even if a hacker can crack the bcrypt code, most likely they won’t know the string you salted on all of your passwords.

**BCrypt is rather slow**

The one drawback of such a large gem is that it slows down your application. It's designed this way to make it harder for hackers to decode your database, but you will have to accept a slower log in speed to your application.


**Takeway**

Overall, BCrypt is a really good choice for passwords.Even if a hacker gets a hold of your database they are going to struggle with turning a hash back into a string.
Even if they ran a list of common words from Webster’s dictionary, it would take them a considerable amount of time to decode anything at all.

**How to add BCrypt to Rails**

Rails wants developers to be able to easily write code that people trust, so that made Bcrypt easy to install.

The following is how to add the gem to your application
Add the ‘bcrypt’ gem to your Gemfile.
Add password_digest to a column in your migration
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



