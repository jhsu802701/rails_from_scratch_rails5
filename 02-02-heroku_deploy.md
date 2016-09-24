# Unit 2
## Chapter 2: Deploying to Heroku

### heroku.sh
* In the project's root path, create the file heroku.sh with the following contents:
```
#!/bin/bash

# STARTING A NEW HEROKU PROJECT:
# 1. Enter the command "heroku login".
# 2. Enter the command "heroku keys:add".
# 3. Enter the command "heroku create".
# 4. In your browser, visit your assigned URL.  You will see the initial Heroku splash screen.
# 5. Enter the command "heroku rename <name>".
# 6. In your browser, visit your new URL.  You will see the initial Heroku splash screen.

# SHOWING WHICH HEROKU PROJECT YOU ARE CURRENTLY CONFIGURED TO PUSH TO:
# git remote -v

# CONNECTING YOUR LOCAL DEVELOPMENT ENVIRONMENT TO A LEGACY HEROKU PROJECT:
# (necessary when you git clone this project or switch from one Heroku project to another):
# 1. Enter the command "heroku login".
# 2. Enter the command "heroku keys:add".
# 3. Enter the command "HEROKU_NAME='<name>' # Please fill in the name of your Heroku project".
# 4. Enter the command "git remote rm heroku".
# 5. Enter the command "git remote add heroku git@heroku.com:$HEROKU_NAME.git".

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
```
* Enter the command "sh heroku.sh".

### Reset
* When you reset your Docker container, you will need to reconfigure it to connect to Heroku again.  Because the process of setting up Heroku is still fresh in your mind, this is a good time to practice how to set up your local development environment for a legacy project instead of a new one.
* Begin following the steps in Unit 1 Chapter 3 for resetting your development environment and downloading the source code.
* After you have downloaded the source code, go to the first tmux screen, cd into the root directory of your project, and enter the command "sh all.sh; sh server.sh". (Remember that the test_code.sh script and tools like RuboCop and Rails Best Practices were not yet in place back in Unit 1 Chapter 3.)
* Follow the steps needed to restore your ability to successfully use the "git commit" command.
* Follow the steps in the heroku.sh script for restoring your ability to push to Heroku again. You should be able to push to Heroku again by entering the command "sh heroku.sh".

### Conclusions

When you git push to the master branch or merge another branch into the master branch, you should run the heroku.sh script afterwards as well in order to deploy your changes.
