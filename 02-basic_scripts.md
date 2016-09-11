# Chapter 2: Basic Scripts

In this chapter, we will add basic scripts to our Rails app.  These scripts will be located in the project's root directory
and allow you to execute basic tasks in only one step.

## A. New Branch
Enter the following command:
```
git checkout -b 2-basic_scripts
```
## B. pg-start.sh
* Create the file pg-start.sh, enter the following contents, and save the file:
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
```
## C.  kill_spring.sh
* Create the file kill_spring.sh, enter the following contents, and save the file:
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
``` 
## D. build_fast.sh
* Create the file build_fast.sh, enter the following contents, and save the file:
```
#!/bin/bash

# After you use the reset.sh script to return the Docker container to a fresh Ruby on Rails 
# environment and use the "git clone" command to download this project's source code, running 
# this script sets up this project AND runs all tests.

# Resetting the Docker container to its original state AND running this script ensures that you
# are on top of all dependencies and can avoid the old "works on my machine" problem.

# This is Joel Spolsky's one-step build process at work.

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
```

## E. server.sh
* Create the file server.sh, enter the following contents, and save the file:
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
* In your Docker container's host environment, open the web browser and go to the appropriate URL.  You should see the Ruby on Rails default splash screen.  Keep this web browser tab open, and refresh it periodically to see the progress in this project.
* Enter the following commands:
```
git status
git add .
git commit -m "Added server.sh"
```

## F. sandbox.sh
*  Create the file sandbox.sh, enter the following contents, and save the file:
```
#!/bin/bash

# This script starts the Rails sandbox.

rails console --sandbox
```
* Save this file.
* Enter the following commands:
```
git status
git add .
git commit -m "Added sandbox.sh"
```
## G. git_check.sh
* Create the file git_check.sh, enter the following contents, and save the file:
```
#!/bin/bash

# Run this script before pushing the branch to GitHub.
# This script enables you to make sure you've covered all bases 
# and shows a list of all new files that will be saved if you do NOT add them to .gitignore.

sh build_fast.sh

echo '----------'
echo 'git status'
git status
```
* Save this file.
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Added git_check.sh"
```

## H.  Wrapping Up
* At this point, this project only provides the default Splash page of Ruby on Rails.  The scripts in this chapter will be
necessary throughout the rest of the project.
* Enter the command "sh git_check.sh".  There should be no new files or changes left to add.
* Enter the command "git push origin 2-basic_scripts".
* Go to the GitHub repository and issue a pull request for this 2-basic_scripts branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
```
