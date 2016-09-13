# Unit 1

## Chapter 4: RuboCop

### New Branch

Enter the command "git checkout -b 01-04-rubocop".

### Gemfile

* Add the following line to the end of the Gemfile:
```
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
