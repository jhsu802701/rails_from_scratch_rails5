# Chapter 2: Basic Scripts

## A.  New Branch
Enter the following command:
```
git checkout -b 2-basic_scripts
```
## B.  pg-start.sh
* In the project's root directory, create the file pg-start.sh, and enter the following contents:
```
echo '--------------'
echo 'sh pg-start.sh'
echo 'Starting PostgreSQL'
echo 'NOTE: This step is necessary if you are using PostgreSQL in a'
echo 'Docker development environment.'
echo 'If you are not using PostgreSQL in the development environment,'
echo 'you can skip this step to save time.'
sudo service postgresql start
```
* Save this file.  It may or may not be necessary later, but it's better to have it and not need it than the other way around.
* Enter the following commands:
```
git add .
git push origin 1-new_app
```
## C.  kill_spring.sh
* In the project's root directory, create the file kill_spring.sh, and enter the following contents:
```
#!/bin/bash

# If you get Spring errors when you attempt to test your app, you need to kill the old Spring process 
# so that a new one can be automatically created to replace it.
pkill -9 -f spring
```
* Save this file.
* Enter the following commands:
```
git add .
git push origin 1-new_app
``` 
## D.  build_fast.sh
* Create the file build_fast.sh and give it the following contents:
```
#!/bin/bash

# After you use the reset.sh script to return the Docker container to a fresh Ruby on Rails environment 
# and use the "git clone" command to download this project's source code, running this script sets up this
# project AND runs all tests.

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
* Save this file.
* Enter the following commands:
```
git add .
git push origin 1-new_app
```

## E.  server.sh
* Create the file server.sh in the project's root directory, and give it the following contents:
```
#!/bin/bash

sh pg-start.sh

echo 'View page at http://localhost:<port number>/'
echo 'If you are developing this app in the host environment, the port number is 3000.'
echo 'If you are using Docker or Vagrant, the port number may be something else.'
echo 'If the host machine of your Docker container is OS X or Windows, you may need to replace "localhost" with a'
echo 'numerical IP address.'

rails server -b 0.0.0.0
```
* Save this file.
* Enter the following commands:
```
git add .
git push origin 1-new_app
```
* In Docker, enter the command "tmux" to create a second simultaneous window.  In one of these tmux windows, enter the command "sh server.sh" to run your local server.  Use a browser in the host machine to see what your Rails app looks like.  Use the other tmux window to enter commands.  If you need to stop the server for any reason, just go to that window and press CTRL-C.

## F.  heroku.sh
* In the project's root path, create the file heroku.sh and give it the following contents:
```
#!/bin/bash

# CONFIGURING YOUR LOCAL MACHINE TO ACCESS HEROKU:
# heroku login
# heroku keys:add

# STARTING A NEW HEROKU PROJECT:
# heroku create
# heroku rename

# Check for name of default Heroku repository:
# git remote -v

# To correct the name of the Heroku repository:
# git remote rm heroku
# git remote add heroku git@heroku.com:<name>.git

# IMPORTANT: HEROKU_NAME must be customized when you use this app as the basis for a new project.
HEROKU_NAME='generic-rails'
# To correct the name of the Heroku repository:
git remote rm heroku
git remote add heroku git@heroku.com:$HEROKU_NAME.git

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
* Save this file.
* Enter the following commands:
```
git add .
git push origin 1-new_app
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
git push origin 1-new_app
```
* OPTIONAL: In Docker, enter the command "tmux" to create an additional simultaneous window.  In one of these tmux windows, enter the command "sh sandbox.sh" to run the sandbox environment.  Use another tmux window to enter commands.
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
git push origin 1-new_app
```
## I.  .gitignore

## J.  Wrapping Up
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
