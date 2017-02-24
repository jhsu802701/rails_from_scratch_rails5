# Unit 8
## Chapter 4: PryRails

### Original Rails Sandbox
* Enter the Rails sandbox environment.
* Enter the command "User.all" and note the messy and unreadable output.
* Enter the command "Admin.all" and note the messy and unreadable output.
* Enter "exit" to leave the Rails sandbox environment.

### Gemfile
* Add the following line to the end of the Gemfile:
```
gem 'pry-rails' # Improves the screen output in the sandbox environment
```
* Enter the command "sh git_check.sh".

### Pry Rails Sandbox
* Enter the Rails sandbox environment.
* Enter the command "User.all" and note the much more readable output.
* Enter the command "Admin.all" and note the much more readable output.
* Enter "exit" to leave the Rails sandbox environment.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added better_errors and binding_of_caller"
git push origin master
sh heroku.sh
```

