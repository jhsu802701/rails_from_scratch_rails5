# Unit 4
## Chapter 4: Code Climate

### Code Climate Web Site
* Log in to the [Code Climate](https://codeclimate.com) web site.  If you have not already done so, you must first create an account.
* On your Code Climate dashboard page, click on "Add Open Source Repo" and submit the name of the GitHub repository you wish to have monitored.
* Download the codeclimate-config.patch file provided, and save it in your project's root directory.

### GPA Rating
* Enter the following commands:
```
patch < codeclimate-config.patch
rm codeclimate-config.patch
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added CodeClimate configuration files"
git push origin master
```
* Code Climate now provides a GPA rating of your code quality and a badge denoting this.

### Code Coverage
* On the project page, click on the "Set Up Test Coverage" button.
* On the Test Setup page, click on the "Ruby" tab.
* Add the following lines to the end of the Gemfile:
```

# Code Climate
gem 'codeclimate-test-reporter', group: :test, require: nil
```
* Replace the beginning of the test/test_helper.rb file (before the Minitest section).  This file should appear as follows:
```
require 'codeclimate-test-reporter'
CodeClimate::TestReporter.start

def run_simple_cov
  require 'simplecov'
  SimpleCov.start 'rails' do
    add_filter 'app/channels/*'
    add_filter 'app/jobs/*'
  end
end

# save to CircleCI's artifacts directory if we're on CircleCI
if ENV['CIRCLE_ARTIFACTS']
  dir = File.join(ENV['CIRCLE_ARTIFACTS'], 'coverage')
  SimpleCov.coverage_dir(dir)
  run_simple_cov
end

run_simple_cov if ENV['EXEC_SIMPLE_COV'] == 'true'

ENV['RAILS_ENV'] ||= 'test'
require File.expand_path('../../config/environment', __FILE__)
require 'rails/test_help'

# BEGIN: Minitest section
...
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added Code Climate gem"
git push origin master
```
* In the Code Coverage instructions, note the value of the CODECLIMATE_REPO_TOKEN variable. 
* Go back to your project page at the Circle CI web site.  Click on "Project Settings", and click on "ENVIRONMENT VARIABLES".  Enter the environment variable name and value specified by Code Climate.
