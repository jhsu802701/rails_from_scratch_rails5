# Unit 8
## Chapter 3: Better Errors

* Add the following lines to the end of the Gemfile:
```
# BEGIN: Better Errors
# Provides more and better information in error pages
group :development do
  gem 'better_errors'
  gem 'binding_of_caller'
end
# END: Better Errors
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added better_errors and binding_of_caller"
git push origin master
```
