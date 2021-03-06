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
* In the Gemfile, add the following lines to the end of the file:
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
  SimpleCov.start do
    add_filter 'app/channels/*'
    add_filter 'app/jobs/*'
    add_filter 'test/*'
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
* You'll see the analysis by the continuous integration services.  The app should pass all tests, but if Circle CI shows that your app has any test failures or errors, this is a warning of a dependency mismanagement problem or an intermittent issue.  (In this case, you should reset your development environment and do some troubleshooting.)
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull origin master
sh heroku.sh
```
* Go back to the project page on Code Climate.  The code coverage test results should be available, and they should match those in your local environment.
* If you don't see the Coverage results, make sure that Circle CI has the correct environment variable and value, and then do a rebuild in Circle CI.
