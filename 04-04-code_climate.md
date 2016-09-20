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
* Add the following lines to the very beginning of test/test_helper.rb
```
require 'codeclimate-test-reporter'
CodeClimate::TestReporter.start
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added Code Climate gem"
git push origin master
```
* In the Code Coverage instructions, note the value of the CODECLIMATE_REPO_TOKEN variable. 
* Go back to the Travis CI web site.  Go to your project, and click on "More Options -> Settings".  Add the environment variable CODECLIMATE_REPO_TOKEN, and enter the value provided by Code Climate.  Click on "Add".
