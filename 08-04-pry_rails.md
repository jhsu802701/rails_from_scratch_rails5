# Unit 8
## Chapter 4: Pry Rails

### Seeding
If you haven't already seeded the database since your latest Docker container reset, enter the command "sh seed.sh".

### Rails Console (1)
* If you haven't already started a Rails console, enter the command "sh sandbox.sh" to enter the Rails console.
* Enter the command "User.first".  You see the parameter values for the first user, but the presentation is a jumbled-up mess.

### Gemfile
* Add the following lines to the end of the Gemfile:
```
# Pry Rails: provides Pry Ruby shell as an alternative to IRB
gem 'pry-rails', group: :development
```
* In the normal command line, enter the command "bundle install".

### Rails Console (2)

* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added better_errors and binding_of_caller"
git push origin master
sh heroku.sh
```
