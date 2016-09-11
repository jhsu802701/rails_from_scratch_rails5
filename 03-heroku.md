# Chapter 3: Heroku

Enter the command "git checkout -b 3-heroku" to start a new branch:

## A. Gemfile
*
*  Enter the command "bundle update".
*  Enter the command "sh git_check.sh".
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
* Follow the instructions in heroku.sh for configuring your local development environment.
* Enter the command "sh heroku.sh" to deploy your project to Heroku.
* Go to the URL of your Heroku app.  You should now see the Rails splash screen instead of the Heroku splash screen.
* Enter the following commands:
```
git status
git add .
git commit -m "Added heroku.sh"
git push origin 2-basic_scripts
