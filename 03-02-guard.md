# Unit 3
## Chapter 2: Guard

### New Branch
Enter the command "git checkout -b 03-02-guard".

### Gemfile
* Add the following lines to the end of the Gemfile:
```
# BEGIN: Guard automated test tools
group :test do
  gem 'guard', '2.14.0'
  gem 'guard-minitest', '2.4.6'
end
# END: Guard automated test tools
```
* Enter the command "bundle install".
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Installed Guard gems"
```
### Configuration
* Create the initial Guardfile by entering the command "bundle exec guard init".
* Replace the initial contents of the Guardfile with the following:

### Wrapping up
* Enter the command "git push origin 03-02-guard".
