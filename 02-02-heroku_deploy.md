# Unit 2
## Chapter 2: Deploying to Heroku

### Configuration
* If you have not already done so, create a free account on [Heroku](https://www.heroku.com/).
* Enter the command "heroku login".
* Enter the command "heroku keys:add".

### Initial Deployment
* Enter the command "heroku create".
* In your browser, visit your assigned URL.  You will see the initial Heroku splash screen, because the Heroku server does not yet have your source code.
* Enter the following commands:
```
HEROKU_TMP="${PWD##*/}"
echo $HEROKU_TMP # Displays the name of your app
```
* Change your app's name (and URL) by entering the command "heroku rename $HEROKU_TMP".
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
```
* To enter your Git and Heroku credentials, enter the command "sh credentials.sh".

### heroku.sh
* In the project's root path, create the file heroku.sh with the following contents:
```
#!/bin/bash

PATH_HEROKU_NAME='config/heroku_name.txt'
HEROKU_NAME=''
if [ -e $PATH_HEROKU_NAME ]
then
  HEROKU_NAME=$(cat $PATH_HEROKU_NAME)
else
  echo 'Enter the name of your app:'
  read HEROKU_NAME
  echo $HEROKU_NAME > $PATH_HEROKU_NAME
  echo ''
fi
echo '-----------------------------'
echo "Heroku app name: $HEROKU_NAME"

echo '--------------------'
echo 'git remote rm heroku'
git remote rm heroku

echo '-----------------------------------------------------'
echo "git remote add heroku git@heroku.com:$HEROKU_NAME.git"
git remote add heroku git@heroku.com:$HEROKU_NAME.git

echo '-------------'
echo 'git remote -v'
git remote -v

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
* Enter the command "echo $HEROKU_TMP" to view the name of your app.
* To push your source code to Heroku, enter the command "sh heroku.sh".  When you are prompted for the name of your app, cut and paste the output of the "echo $HEROKU_TMP" command.
* Note that the name of your app on Heroku is stored at config/heroku_name.txt.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added script and config file for deploying to Heroku"
git push origin master
```
* Enter the command "sh heroku.sh".
* To see how the heroku.sh script asks for the name of your app, enter the command "rm config/heroku_name.txt; sh heroku.sh".
* Enter "git status".  If you entered the name of your app correctly, you'll see no mention of changes in config/heroku_name.txt.

### Reset
* Begin following the steps in Unit 1 Chapter 3 for resetting your development environment and downloading the source code.
* After you have downloaded the source code, go to the first tmux screen, cd into the root directory of your project, and enter the command "sh credentials.sh; sh all.sh; sh server.sh".
* While you wait for the previous step to be completed, open a second tmux window for entering commands.
* After everything has been set up, enter the command "sh heroku.sh".  There's nothing new to deploy, but if all goes well, you'll see that you can connect to your app on Heroku.

### Conclusions

When you git push to the master branch or merge another branch into the master branch, you should run the heroku.sh script afterwards as well in order to deploy your changes.
