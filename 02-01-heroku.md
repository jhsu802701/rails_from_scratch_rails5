# Unit 2
# Chapter 1: Heroku

In this chapter, you will deploy the project to Heroku.  Do NOT create a new Git branch in this chapter.  Remain in the master branch, because that is what will be deployed to Heroku.

## A. Gemfile
* Heroku will not accept your attempt to deploy if your master branch does not specify the use of the pg (PostgreSQL) gem in production.  (As you can see in the original Gemfile, the pg gem is not specified.)
* Replace everything up to the end of the "initial gems" section of the Gemfile with the following code:
```
# Installing a version of Ruby manually requires a long wait.
# Preinstall the newest version in this project's Docker image before updating the Ruby version.
# NOTE: You should specify a Ruby version
# It is commented out for your convenience.
# Enter the command "rbenv versions" to see which versions of Ruby are already installed locally.
# ruby '2.3.1'

source 'https://rubygems.org'

#####################
# BEGIN: initial gems
#####################

# Because the rails, pg, and nokogiri gems take a long time to install
# the versions are unspecified here for your convenience.
# You should specify versions.
# Enter the following command to see which versions you have installed locally:
# gem list "^rails$"; gem list pg; gem list nokogiri
gem 'rails'
gem 'pg' # Not necessary in development and testing if you're using SQLite
gem 'nokogiri'

# BEGIN: SQLite
group :development, :test do
  gem 'sqlite3', '1.3.11' # Delete if using only PostgreSQL
end
# END: SQLite

gem 'puma', '3.6.0' # Server
gem 'sass-rails', '5.0.6' # Stylesheets
gem 'uglifier', '3.0.2' # Compressor for JavaScript assets
gem 'coffee-rails', '4.2.1' # Use CoffeeScript for .coffee assets and views
gem 'jquery-rails', '4.2.1' # Use jquery as the JavaScript library
gem 'turbolinks', '5.0.1' # Makes navigating your web application faster
gem 'jbuilder', '2.6.0' # Build JSON APIs with ease

group :development, :test do
  gem 'byebug', '9.0.5', platform: :mri # Call 'byebug' anywhere in the code to stop execution and get a debugger console
end

group :development do
  # Access an IRB console on exception pages or by using <%= console %> anywhere in the code.
  gem 'web-console', '3.3.1'
  gem 'listen', '3.0.8'
  # Spring speeds up development by keeping your application running in the background. Read more: https://github.com/rails/spring
  gem 'spring', '1.7.2'
  gem 'spring-watcher-listen', '2.0.0'
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
####################
# END: original gems
####################
```
*  The pg gem (PostgreSQL) is needed in the production environment for Heroku, because it does NOT offer SQLite.  In the production environment, PostgreSQL is greatly preferred over SQLite.  While it's usually best to use the same database in the development and production environments, the use of SQLite in development and PostgreSQL in production (for Heroku) is usually used in tutorials.  If your production environment is not Heroku, it's best to use PostgreSQL in the development and testing environments and skip the use of SQLite completely.
*  Enter the command "bundle update".  This installs the latest versions of the above gems that conform to the specified requirements.  The Gemfile.lock file is automatically replaced.
*  NOTE: The gems will need updating periodically.
*  Enter the following commands: 
```
sh git_check.sh # Gemfile and Gemfile.lock have changed
git add .
git commit -m "Updated Gemfile for Heroku"
git push origin master
```

## B. heroku.sh
* In the project's root path, create the file heroku.sh and give it the following contents before saving it:
```
#!/bin/bash

# CONFIGURING YOUR LOCAL DEVELOPMENT ENVIRONMENT TO ACCESS HEROKU:

# heroku login
# heroku keys:add

# STARTING A NEW HEROKU PROJECT:

# heroku create
# In your browser, visit your assigned URL.  You will see the initial Heroku splash screen.
# heroku rename <name>
# In your browser, visit your new URL.  You will see the initial Heroku splash screen.

# FIND OUT WHICH HEROKU PROJECT YOU ARE CURRENTLY CONFIGURED TO PUSH TO:

# git remote -v

# SET THE NAME OF THE HEROKU REPOSITORY:
# (necessary when you git clone this project or switch from one Heroku project to another):

# HEROKU_NAME='<name>'
# git remote rm heroku
# git remote add heroku git@heroku.com:<name>.git

echo '----------------------'
echo 'git push heroku master'
git push heroku master

echo '---------------------------'
echo 'heroku run rails db:migrate'
heroku run rails db:migrate

echo '-------------'
echo 'git remote -v'
git remote -v
```
* Create your own free account on Heroku if you do not already have one.
* Follow the instructions in heroku.sh for configuring your local development environment and starting the new Heroku project.
* Enter the command "sh heroku.sh" to deploy your project to Heroku.
* Go to the URL of your Heroku app.  You should now see the "Welcome to public/index.html!" screen instead of the Heroku splash screen.  If you didn't already have the index.html file in your public directory, you would see an error message.  For some reason, Heroku does not display the initial Rails splash screen like your local server does.
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Added heroku.sh"
git push origin master
```
