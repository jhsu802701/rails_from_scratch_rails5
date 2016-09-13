# Unit 1

## Chapter 4: RuboCop

RuboCop is a Ruby code analyzer that points out violations of the Ruby Style Guide.  Violations don't cause the code to fail but can slow down expert Rubyists when they read and analyze the code.  It's best to add RuboCop very early in a project, because it gets increasingly difficult to introduce later on.

### New Branch

Enter the command "git checkout -b 01-04-rubocop".

### Gemfile

* Add the following line to the end of the Gemfile:
```
# BEGIN: gems used in test_code.sh script
gem 'rubocop', '0.42.0' # Code style checking tool; not recommended for legacy apps
```
* Enter the command "bundle install; rubocop".  This installs and runs Rubocop.  
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Installed Rubocop"
```

### git_check.sh

### .rubocop.yml

### Wrapping Up
