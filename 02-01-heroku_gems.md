# Unit 2
# Chapter 1: Gems for Heroku

In this chapter, you will deploy the project to Heroku.  Do NOT create a new Git branch in this chapter.  Remain in the master branch, because that is what will be deployed to Heroku.

### New Branch
Enter the command "git checkout -b 02-01-A-heroku"

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
* Stop all processes in your tmux screens. Enter "exit" to close each tmux screen. Enter "exit" to leave the Docker container.
* When you are out of the Docker container, enter "sh reset.sh" to run the Docker container reset script.
* On your local machine, delete your project's source code.
* Go to your GitHub repository for your project, and click on the "Clone or download" button to look up your project's GitHub URL.
* In the /home/winner/shared directory in your Docker container, enter the command "git clone (GitHub URL)". This downloads your code.
* In the Docker container, start tmux.
* In your project's root directory, enter the command "git checkout 02-01-A-heroku".  This brings you into the alternate branch you created and pushed to.
* Enter the command "sh all.sh; sh server.sh" to execute the build process and start the local server.
* Once the local server is running, open your web browser and view your project.
* Open a second tmux window, cd into your project directory, and use this window for entering commands.
* If all went well, you are ready to merge this branch into the master branch.

### 

* Reconfigure Git by entering the following commands (and providing your GitHub email address and name, respectively):
```
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

### heroku.sh
* In the project's root path, create the file heroku.sh and give it the following contents before saving it:
```
#!/bin/bash

# STARTING A NEW HEROKU PROJECT:
# 1. heroku login
# 2. heroku keys:add
# 3. heroku create
# 4. In your browser, visit your assigned URL.  You will see the initial Heroku splash screen.
# 5. heroku rename <name>
# 6. In your browser, visit your new URL.  You will see the initial Heroku splash screen.

# SHOW WHICH HEROKU PROJECT YOU ARE CURRENTLY CONFIGURED TO PUSH TO:
# git remote -v

# CONNECTING YOUR LOCAL DEVELOPMENT ENVIRONMENT TO A LEGACY HEROKU PROJECT:
# (necessary when you git clone this project or switch from one Heroku project to another):
# 1. heroku login
# 2. heroku keys:add
# 3. HEROKU_NAME='<name>' # Please fill in the name of your Heroku project
# 4. git remote rm heroku
# 5. git remote add heroku git@heroku.com:$HEROKU_NAME.git

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
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added heroku.sh"
git push origin master
sh heroku.sh
```

### Conclusions

When you git push to the master branch or merge another branch into the master branch, you should run the heroku.sh script afterwards as well.
