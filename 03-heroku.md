# Chapter 3: Heroku

Enter the command "git checkout -b 3-heroku" to start a new branch.  In this chapter, we will deploy the project to Heroku.

## A. Gemfile
*  Replace the Gemfile with the following contents:
```
# Installing a version of Ruby manually requires a long wait.
# Preinstall the newest version in this project's Docker image before updating the Ruby version.
ruby '2.3.1'

source 'https://rubygems.org'

######################
# BEGIN: original gems
######################

# Because the rails, pg, and nokogiri gems take a long time to install, this app initially uses
# the current versions used in the Rails Tutorial Sample App.
# These are preinstalled in the rbenv-general Docker images.
gem 'rails', '5.0.0.1'
gem 'pg', '0.18.4' # Not necessary in development and testing if you're using SQLite
gem 'nokogiri', '1.6.8'

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
  gem 'byebug', platform: :mri # Call 'byebug' anywhere in the code to stop execution and get a debugger console
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
*  The pg gem (PostgreSQL) is needed in the production environment for Heroku, because it does NOT offer SQLite.  In the production environment, PostgreSQL is greatly preferred over SQLite.  While it's usually best to use the same database in the development and production environments, the use of SQLite in development and PostgreSQL in production (for Heroku) is usually used in tutorials.  If your production environment is not Heroku, it's best to use PostgreSQL in the development environment.
*  Enter the command "bundle update".  This installs the latest versions of the above gems that conform to the specified requirements.  The Gemfile.lock file is automatically replaced.
*  NOTE: The gems will need updating periodically.
*  Enter the following commands: 
```
sh git_check.sh # Gemfile and Gemfile.lock have changed
git add .
git commit -m "Updated Gemfile for Heroku"
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
# In your browser, visit your assigned URL.
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
* Follow the instructions in heroku.sh for configuring your local development environment.
* Enter the command "sh heroku.sh" to deploy your project to Heroku.
* Go to the URL of your Heroku app.  You should now see the Rails splash screen instead of the Heroku splash screen.
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Added heroku.sh"
git push origin 3-heroku
