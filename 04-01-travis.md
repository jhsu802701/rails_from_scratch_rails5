# Unit 4
## Chapter 1: Travis CI

### Setting Up On the Travis CI Web Site
* Log in to the [Travis CI](https://travis-ci.org/) web site.  If you haven't already done so, you need to create an account first.
* Go to your Accounts page.
* Select your Rails app.  All projects monitored by Travis CI have a green check mark next to them.

### .travis.yml
* Create the file .travis.yml with the following content:
```
language: ruby
rvm:
 - '2.3.1'

script:
  - bin/rails db:migrate RAILS_ENV=test
```
* Enter the command "sh git_check.sh"
* Enter the following commands:
```
git add .
git commit -m "Added travis.yml"
git push origin master
```
