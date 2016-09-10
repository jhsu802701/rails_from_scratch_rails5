# Chapter 2: Basic Scripts

## A.  New Branch
Enter the following command:
```
git checkout -b 2-basic_scripts
```
## B.  pg-start.sh
* In the project's root directory, create the file pg-start.sh, and enter the following contents before saving it:
```
#!/bin/bash

echo '--------------'
echo 'sh pg-start.sh'
echo 'Starting PostgreSQL'
echo 'NOTE: This step is necessary if you are using PostgreSQL in a'
echo 'Docker development environment.'
echo 'If you are not using PostgreSQL in the development environment,'
echo 'you can skip this step to save time.'
sudo service postgresql start
```
* Enter the command "sh pg-start.sh" to run this command.  You'll see a system message notifying you that PostgreSQL is starting up.  If you decide to change your development and testing database from SQLite to PostgreSQL, starting up PostgreSQL will be necessary in a Docker container, because the PostgreSQL server does NOT automatically start.
* Enter the following commands:
```
git status
git add .
git commit -m "Added pg-start.sh"
git push origin 2-basic_scripts
```
## C.  kill_spring.sh
* In the project's root directory, create the file kill_spring.sh, and enter the following contents before saving it:
```
#!/bin/bash

# If you get Spring errors when you attempt to test your app, you need to kill the old Spring 
# process so that a new one can be automatically created to replace it.

pkill -9 -f spring
```
* Enter the command "sh kill_spring.sh" to try out this script.
* Enter the following commands:
```
git status
git add .
git commit -m "Added kill_spring.sh"
git push origin 2-basic_scripts
``` 
## D.  build_fast.sh
* Create the file build_fast.sh and give it the following contents before saving it:
```
#!/bin/bash

# After you use the reset.sh script to return the Docker container to a fresh Ruby on Rails 
# environment and use the "git clone" command to download this project's source code, running 
# this script sets up this project AND runs all tests.

# Resetting the Docker container to its original state AND running this script ensures that you
# are on top of all dependencies and can avoid the old "works on my machine" problem.

sh pg-start.sh

echo '--------------'
echo 'bundle install'
bundle install

echo '-----------------'
echo 'sh kill_spring.sh'
sh kill_spring.sh

echo '---------------------------'
echo 'bundle exec rake db:migrate'
bundle exec rake db:migrate

echo '-----------------'
echo 'sh kill_spring.sh'
sh kill_spring.sh

echo '---------------------'
echo 'bundle exec rake test'
bundle exec rake test
```
* Enter the command "sh build_fast.sh" to run this command.
* Enter the following commands:
```
git status # db/schema.rb is created.
git add .
git commit -m "Added build_fast.sh"
git push origin 2-basic_scripts
```

## E.  server.sh
* Create the file server.sh in the project's root directory, and give it the following contents before saving it:
```
#!/bin/bash

sh pg-start.sh

echo 'View page at http://localhost:<port number>/'
echo 'If you are developing this app in the host environment, the port number is 3000.'
echo 'If you are using Docker or Vagrant, the port number may be something else.'
echo 'If the host machine of your Docker container is OS X or Windows, you may need to replace "localhost" with a'
echo 'numerical IP address (the one used by Docker Machine).'

rails server -b 0.0.0.0
```
* In Docker, enter the command "tmux" to begin tmux mode.  Press Ctrl-b and then "c" to create a second tmux window.  In one of your tmux screen, cd into your project's root directory, and enter the command "sh server.sh" to run your local server.  Use a browser in the host machine to see what your Rails app looks like.  Use the other tmux screen to continue entering commands.  Press Ctrl-b and then "p" to go to the previous tmux screen.  Press Ctrl-b and then "n" to go to the next tmux
screen.  To scroll within a tmux screen, press Ctrl-b and then your normal keys for paging up and paging down.  To exit scroll mode (which disables your ability to do anything other than scroll up or down), press Ctrl-C.   If you need to stop the server for any reason, just go to that tmux screen and press CTRL-c.  To close a tmux screen, go to that screen and enter "exit".
* Enter the following commands:
```
git status
git add .
git commit -m "Added server.sh"
git push origin 2-basic_scripts
```

## F.  heroku.sh
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
# In your browser, visit your new URL.

# FIND OUT WHICH HEROKU PROJECT YOU ARE CURRENTLY CONFIGURED TO PUSH TO

# git remote -v

# SET THE NAME OF THE HEROKU REPOSITORY
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
* Follow the instructions in heroku.sh for configuring your local development environment.
* Enter the command "sh heroku.sh" to deploy your project to Heroku.
* Enter the following commands:
```
git status
git add .
git commit -m "Added heroku.sh"
git push origin 2-basic_scripts
```
## G.  sandbox.sh
* In the project's root directory, create the file sandbox.sh and add the following contents:
```
#!/bin/bash

# This script starts the Rails sandbox.

rails console --sandbox
```
* Save this file.
* Enter the following commands:
```
git add .
git push origin 2-basic_scripts
```
## H.  git_check.sh
* In the project's root directory, create the file git_check.sh and add the following contents:
```
#!/bin/bash

# Run this script before updating Git.  This script enables you to make sure you've covered all bases and shows a list of all new files that will be saved if you do NOT add them to .gitignore.

sh build_fast.sh
git status
```
* Save this file.
* Enter the following commands:
```
git add .
git push origin 2-basic_scripts
```
## H.  .gitignore

## I.  Wrapping Up
* At this point, this project only provides the default Splash page of Ruby on Rails.  The scripts in this chapter will be
necessary throughout the rest of the project.
* Set up Heroku.  (The heroku_upload.sh script doubles as a cheat sheet.)  Then enter "sh heroku_upload.sh" to upload this app
to Heroku and then view it.
* Enter the command "sh git_check.sh".  There should be no new files or changes left to add.
* Go to the GitHub repository and issue a pull request for this 1-basic_scripts branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
```
