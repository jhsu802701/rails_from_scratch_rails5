# Unit 2
## Chapter 2: Deploying to Heroku

### Configuration
* If you have not already done so, create an account on [Heroku](https://www.heroku.com/).
* Enter the command "heroku login".
* Enter the command "heroku keys:add".

### Initial Deployment
* Enter the command "heroku create".
* In your browser, visit your assigned URL.  You will see the initial Heroku splash screen, because the Heroku server does not yet have your source code.
* Change your app's name (and URL) by entering the command "heroku rename (name)".
* In your browser, visit your new URL.  You will again see the initial Heroku splash screen.
* To see which Heroku project you are currently configured to push to, enter the command "git remote -v".
* To deploy your app, enter the following commands:
```
git push heroku master
heroku run rails db:migrate
```
* In your web browser, refresh the tab with your app's Heroku URL.  Now your app has replaced the Heroku splash screen.

### credentials.sh
Add the following lines to the end of the credentials.sh file:
```
echo '------------'
echo 'heroku login'
heroku login

echo '---------------'
echo 'heroku keys:add'
heroku keys:add

echo
echo "Enter your app's name on Heroku:"
read APP_NAME

HEROKU_NAME=$APP_NAME
echo '--------------------'
echo 'git remote rm heroku'
git remote rm heroku

echo '-----------------------------------------------------'
echo "git remote add heroku git@heroku.com:$HEROKU_NAME.git"
git remote add heroku git@heroku.com:$HEROKU_NAME.git

echo '-------------'
echo 'git remote -v'
git remote -v
```

### heroku.sh
* In the project's root path, create the file heroku.sh with the following contents:
```
#!/bin/bash

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
