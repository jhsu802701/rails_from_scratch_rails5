# Unit 2
# Chapter 1: Gems for Heroku

In this chapter, you will revise the Gemfile.  The main change is the addition of the pg (PostgreSQL) gem for the production environment.  If you do not make this change, you will not be able to deploy your app to Heroku.

### New Branch
Enter the command "git checkout -b 02-01-heroku_gems"

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
gem 'nokogiri'
gem 'pg' # Not necessary in development and testing if you're using SQLite
gem 'rails'

# BEGIN: SQLite
# NOTE: This section is automatically deleted in the pg_setup.rb script
group :development, :test do
  gem 'sqlite3' # Delete if using only PostgreSQL
end
# END: SQLite

gem 'coffee-rails' # Use CoffeeScript for .coffee assets and views
gem 'jbuilder' # Build JSON APIs with ease
gem 'jquery-rails' # Use jquery as the JavaScript library
gem 'puma' # Server
gem 'sass-rails' # Stylesheets
gem 'turbolinks' # Makes navigating your web application faster
gem 'uglifier' # Compressor for JavaScript assets

# Call 'byebug' anywhere in the code to stop execution and get a debugger console
group :development, :test do
  gem 'byebug', platform: :mri
end

group :development do
  gem 'listen'
  # Access an IRB console on exception pages or by using <%= console %> anywhere in the code.
  gem 'web-console'
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
*  Enter the command "sh git_check.sh".
*  Enter the following commands: 
```
git add .
git commit -m "Updated Gemfile for Heroku"
git push origin 02-01-heroku_gems
```
### Reset
* While you have not removed gems from the Gemfile, it's still a good idea at this point to reset everything to guard against dependency mismanagement risks.  If there are any problems, it's easier to resolve things now than later.
* Begin following the steps in Unit 1 Chapter 3 for resetting your development environment and downloading the source code.
* After you have downloaded the source code, go to the first tmux screen, cd into the root directory of your project, and enter the command "git checkout 02-01-heroku_gems".  This switches you from the master branch to the 02-01-heroku_gems branch and downloads the source code revisions of this alternate branch.
* Enter your app's root directory, and enter the command "sh all.sh; sh server.sh". (Remember that the test_code.sh script and tools like RuboCop and Rails Best Practices were not yet in place back in Unit 1 Chapter 3.)
* While you wait for the setup process to complete, open a second tmux window, go to your app's root directory, and enter the command "sh credentials.sh".
* Once the local server is running, open your web browser and view your project.
* If your project passed all tests, you are able to view your local app in the browser, and the outcome of running the all.sh script was as expected, then this means that you have covered all bases through the Docker image build scripts and the build_fast.sh script.

### Wrapping Up 
* Go to the GitHub repository and click on the "Compare and pull request" button for this 02-01-heroku_gems branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull origin master
```
