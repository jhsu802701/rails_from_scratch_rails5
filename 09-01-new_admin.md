# Unit 9
## Chapter 1: New Admins

In this chapter, you will add scripts for creating a new admin.  Because the admin role is not intended to be available to the general public, scripts are used for creating new admins.

### New Branch
Enter the command "git checkout -b 09-01-new_admin".

### admin_create.rb
* Create the file create_admin.rb with the following content:
```
#!/usr/bin/env ruby

arg_username = ''
arg_email = ''
arg_first_name = ''
arg_last_name = ''
arg_pwd = ''
arg_pwd_conf = ''

puts '***********************'
puts 'Creating the admin user'

while arg_username == ''
  puts
  puts 'Enter the username of the new admin:'
  arg_username = gets.chomp
end

while arg_email == ''
  puts
  puts 'Enter the email address of the new admin:'
  arg_email = gets.chomp
end

while arg_first_name == ''
  puts
  puts 'Enter the first name of the new admin:'
  arg_first_name = gets.chomp
end

while arg_last_name == ''
  puts
  puts 'Enter the last name of the new admin:'
  arg_last_name = gets.chomp
end

while arg_pwd == ''
  puts
  puts
  puts 'Using the same password for all of your accounts is risky.'
  puts 'Limiting yourself to passwords that you can easily remember is risky.'
  puts 'You should use a password management program like KeePassX'
  puts '(http://www.keepassx.org/) to create much better passwords AND '
  puts 'store them in encrypted form.'
  puts
  puts 'Enter the password of the new admin:'
  arg_pwd = gets.chomp
end

while arg_pwd_conf == ''
  puts
  puts 'Confirm the password of the new admin:'
  arg_pwd_conf = gets.chomp
end

puts
puts 'DEFAULT: no'
puts 'Do you wish to make this user a superadmin?'
puts "Enter 'Y' or 'y' to answer yes."
arg_super = gets.chomp

puts
if arg_super == 'y' || arg_super == 'Y'
  Admin.create!(username: arg_username, last_name: arg_last_name,
                first_name: arg_first_name, email: arg_email,
                password: arg_pwd, password_confirmation: arg_pwd_conf,
                super: true, confirmed_at: Time.now)
  puts 'Admin type: super'
else
  Admin.create!(username: arg_username, last_name: arg_email,
                first_name: arg_first_name, email: arg_email,
                password: arg_pwd, password_confirmation: arg_pwd_conf,
                super: false, confirmed_at: Time.now)
  puts 'Admin type: regular'
end
puts "Username: #{arg_username}"
puts "First name: #{arg_first_name}"
puts "Last name: #{arg_last_name}"
puts "Email: #{arg_email}"
puts "Password: #{arg_pwd}"
```
* Enter the following commands:
```
git add .
git commit -m "Added admin_create.rb"
```

### admin_create.sh
* Create the file admin_create.sh with the following content:
```
#!/bin/bash

sh pg-start.sh

echo '**********************'
echo 'Entering rails console'
bundle exec rails runner "eval(File.read 'admin_create.rb')"
```
* Enter the command "sh admin_create.sh" to create a new admin.  You can use the default values.
* Use the SQLiteBrowser tool in your host environment to verify that you have created the new admin.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added admin_create.sh"
git push origin 09-01-new_admin
```

### Wrapping Up
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
