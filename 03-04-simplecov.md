# Unit 3
## Chapter 4: SimpleCov

In this chapter, you will add the SimpleCov gem, which provides coverage reports.

### New Branch
Enter the command "git checkout -b 03-04-simplecov".

### Gemfile
* Add the following lines to the end of the Gemfile:
```
# BEGIN: SimpleCov (provides % coverage)
group :test do
  gem 'simplecov', '0.12.0'
end
# END: SimpleCov (provides % coverage)
```
* Enter the command "bundle install".
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Installed simplecov gem"
```
### Configuration
* Add the following lines to the BEGINNING of the test/test_helper.rb file:
```
if ENV['EXEC_SIMPLE_COV'] == 1
  require 'simplecov'
  SimpleCov.start 'rails'
  puts "required simplecov"
end
```
* In the file build_fast.sh, replace the "rails test" section with the following code:
```
# Provide coverage report
echo '----------------------------'
echo 'rails test EXEC_SIMPLE_COV=1'
rails test EXEC_SIMPLE_COV=1
```
* Add "coverage" to the .gitignore file.  This prevents the coverage reports from entering source control.
* Enter the command "sh git_check.sh".

### Wrapping up
* Enter the command "git push origin 03-04-simplecov".
