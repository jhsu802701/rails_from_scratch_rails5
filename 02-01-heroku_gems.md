# Unit 2
# Chapter 1: Gems for Heroku

In this chapter, you will deploy the project to Heroku.  Do NOT create a new Git branch in this chapter.  Remain in the master branch, because that is what will be deployed to Heroku.

### New Branch
Enter the command "git checkout -b 02-01-heroku"

### Gemfile
* Heroku will not accept your attempt to deploy if your master branch does not specify the use of the pg (PostgreSQL) gem in production.  (As you can see in the original Gemfile, the pg gem is not specified.)
* Replace everything up to the end of the "initial gems" section of the Gemfile with the following code:
```
# Installing a version of Ruby manually requires a long wait.
# Preinstall the newest version in this project's Docker image before updating the Ruby version.
# NOTE: You should specify a Ruby version.  It is commented out for your convenience.
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
# NOTE: This section is automatically deleted in the pg-setup.rb script
group :development, :test do
  gem 'sqlite3' # Delete if using only PostgreSQL
end
# END: SQLite

gem 'puma' # Server
gem 'sass-rails' # Stylesheets
gem 'uglifier' # Compressor for JavaScript assets
gem 'coffee-rails' # Use CoffeeScript for .coffee assets and views
gem 'jquery-rails' # Use jquery as the JavaScript library
gem 'turbolinks' # Makes navigating your web application faster
gem 'jbuilder' # Build JSON APIs with ease

# Call 'byebug' anywhere in the code to stop execution and get a debugger console
group :development, :test do
  gem 'byebug', platform: :mri
end

group :development do
  # Access an IRB console on exception pages or by using <%= console %> anywhere in the code.
  gem 'web-console'
  gem 'listen'
  # Spring speeds up development by keeping your application running in the background.
  # Read more at https://github.com/rails/spring .
  gem 'spring'
  gem 'spring-watcher-listen'
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
###################
# END: initial gems
###################
```
*  The pg gem (PostgreSQL) is needed in the production environment for Heroku, because it does NOT offer SQLite.  In the production environment, PostgreSQL is greatly preferred over SQLite.  While it's usually best to use the same database in the development and production environments, the use of SQLite in development and PostgreSQL in production (for Heroku) is usually used in tutorials.  If your production environment is not Heroku, it's best to use PostgreSQL in the development and testing environments and skip the use of SQLite completely.
*  Enter the following commands: 
```
sh git_check.sh # Gemfile and Gemfile.lock have changed
git add .
git commit -m "Updated Gemfile for Heroku"
git push origin 02-01-heroku
```
### Reset
* While you have not actually changed the list of gems in your app (because you only rearranged the Gemfile and didn't delete gems), it's still a good idea at this point to reset everything to guard against dependency mismanagement risks.  If there are any problems, it's easier to resolve things now than later.
* Begin following the steps in Unit 1 Chapter 3 for resetting your development environment and downloading the source code.
* After you have downloaded the source code, go to the first tmux screen, cd into the root directory of your project, and enter the command "sh all.sh; sh server.sh". (Remember that the test_code.sh script and tools like RuboCop and Rails Best Practices were not yet in place back in Unit 1 Chapter 3.)
* Follow the steps needed to restore your ability to successfully use the "git commit" command.
* Follow the steps in the heroku.sh script for restoring your ability to push to Heroku again. You should be able to push to Heroku again by entering the command "sh heroku.sh".

### 

* Reconfigure Git by entering the following commands (and providing your GitHub email address and name, respectively):
```
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```


