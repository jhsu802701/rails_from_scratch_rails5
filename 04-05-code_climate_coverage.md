# Unit 4
## Chapter 5: Code Climate Coverage Test

### New Branch
Enter the command "git checkout -b 04-05-code_climate_coverage".

### Code Climate and Circle CI Instructions
* On the Code Climate project page, click on the "Set Up Test Coverage" button.
* On the Test Setup page, click on the "Ruby" tab to see the instructions for setting up test coverage.  (The procedure you will use here has some differences from the procedures provided by Code Climate.)
* There are no instructions for getting test coverage data from Circle CI, but the procedure is actually easier than the ones for working with Travis, Solano Labs, and Semaphore.
* In the Code Coverage instructions, note the value of the CODECLIMATE_REPO_TOKEN variable.
* Go back to your project page at the Circle CI web site. Click on "Project Settings", and click on "ENVIRONMENT VARIABLES". Enter the environment variable name and value specified by Code Climate.

### Gemfile
* In the Gemfile, replace the SimpleCov section with the following code:
``` 

# Code Climate
gem 'codeclimate-test-reporter', group: :test, require: nil
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added codeclimate-test-reporter gem"
```
## test/test_helper.rb
* Replace the SimpleCov section of the test/test_helper.rb file with the following code:
```
# BEGIN: SimpleCov/Code Climate
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

# Used in the development environment by certain scripts
run_simple_cov if ENV['EXEC_SIMPLE_COV'] == 'true'

ENV['RAILS_ENV'] ||= 'test'
require File.expand_path('../../config/environment', __FILE__)
require 'rails/test_help'

# End: SimpleCov/Code Climate
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added Code Climate gem"
git push origin 04-05-code_climate_coverage
```
### Wrapping Up
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull origin master
```
* Go back to the project page on Code Climate.  The code coverage test results should be available, and they should match those in your local environment.
