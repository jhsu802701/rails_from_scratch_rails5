# Unit 3
## Chapter 4: SimpleCov

In this chapter, you will add the SimpleCov gem, which provides coverage reports.

### New Branch
Enter the command "git checkout -b 03-04-simplecov".

### Gemfile
* Add the following lines to the end of the Gemfile:
```

# SimpleCov (provides % coverage)
gem 'simplecov', require: false, group: :test
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Installed simplecov gem"
```
### Configuration
* Add the following lines to the BEGINNING of the test/test_helper.rb file:
```
# BEGIN: SimpleCov
if ENV['EXEC_SIMPLE_COV'] == 'true'
  require 'simplecov'
  SimpleCov.start 'rails' do
    add_filter 'app/channels/*'
    add_filter 'app/jobs/*'
  end
  puts 'required simplecov'
end
# END: SimpleCov
```
* In the file build_fast.sh, replace the "rails test" section with the following code:
```
# Provide coverage report
echo '---------------------------------'
echo "EXEC_SIMPLE_COV='true' rails test"
EXEC_SIMPLE_COV='true' rails test
```
* Add the following lines to the end of .gitignore:
```
# Omit SimpleCov coverage reports
coverage
```
* Enter the command "sh git_check.sh".  Don't worry about the lack of coverage for the application controller, helper, mailer, and controller just yet.  These topics will be tackled later.
* Enter the following commands:
```
git add .
git commit -m "Configured SimpleCov"
git push origin 03-04-simplecov
```

### Wrapping up
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
