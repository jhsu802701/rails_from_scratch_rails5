# Unit 8
## Chapter 4: Pry Rails

* Add the following lines to the end of the Gemfile:
```
# Pry Rails: provides Pry Ruby shell as an alternative to IRB
gem 'pry-rails', group: :testing
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added better_errors and binding_of_caller"
git push origin master
sh heroku.sh
```
