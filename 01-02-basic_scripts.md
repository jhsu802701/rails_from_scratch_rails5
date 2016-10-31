# Unit 1
## Chapter 2: Basic Scripts

In this chapter, we will add basic scripts for executing routine tasks in only one step.  These scripts will be located in the project's root directory and allow you to execute basic tasks in only one step.  I find these scripts to be necessary, because a Rails project has more tasks and details than I can remember verbatim.

### New Branch
Enter the following command:
```
git checkout -b 01-02-basic_scripts
```
### pg-start.sh
* Create the file pg-start.sh with the following content:
```
#!/bin/bash

# NOTE: This step is necessary if you are using PostgreSQL in a Docker development environment.
# If you are not using PostgreSQL in the development environment, you can skip this step to save time.

echo '-----------------------------'
echo 'sudo service postgresql start'
sudo service postgresql start
```
* Enter the command "sh pg-start.sh" to run this command.  You'll see a system message notifying you that PostgreSQL is starting up.  If you decide to change your development and testing database from SQLite to PostgreSQL, starting up PostgreSQL will be necessary in a Docker container, because the PostgreSQL server does NOT automatically start.
* While this script is not necessary if you're using SQLite instead of PostgreSQL for the database in the development and test environments, many projects skip the use of SQLite and use PostgreSQL exclusively.  It's better to have this script and not need it than to need it but not have it.  It's easier to remove this feature later than to add it later.
* Enter the following commands:
```
git status
git add .
git commit -m "Added pg-start.sh"
```

### kill_spring.sh
* Create the file kill_spring.sh with the following content:
```
#!/bin/bash

# If you get Spring errors when you attempt to test your app,
# you need to kill the old Spring process so that a new one
# can be automatically created to replace it.

echo '------------------'
echo 'pkill -9 -f spring'
pkill -9 -f spring
```
* Enter the command "sh kill_spring.sh" to try out this script.
* Enter the following commands:
```
git status
git add .
git commit -m "Added kill_spring.sh"
``` 
### build_fast.sh
* Create the file build_fast.sh with the following content:
```
#!/bin/bash

# After you use the reset.sh script to return the Docker container to a
# fresh Ruby on Rails environment and use the "git clone" command to 
# download this project's source code, running this script sets up this 
# project AND runs all tests.

# Resetting the Docker container to its original state AND running this 
# script ensures that you are on top of all dependencies and can avoid 
# being stopped in your tracks by the infamous "works on my machine" 
# problem.

# This is Joel Spolsky's one-step build process at work.

# Killing the spring server is usually not necessary, but it's better to 
# include this script and not need it than to need it but not have it.

sh pg-start.sh

echo '--------------'
echo 'bundle install'
bundle install

sh kill_spring.sh

echo '----------------'
echo 'rails db:migrate'
rails db:migrate

echo '-----------------'
echo 'sh kill_spring.sh'
sh kill_spring.sh

echo "\n\n\n\n\n\n\n\n\n\n"

echo '----------'
echo 'rails test'
rails test
```
* Enter the command "sh build_fast.sh" to run this command.
* Enter the following commands:
```
git status # db/schema.rb is created.
git add .
git commit -m "Added build_fast.sh"
```

### server.sh
* Create the file server.sh with the following content:
```
#!/bin/bash

sh pg-start.sh

echo 'View page at http://localhost:3000/'
echo 'If you are using Docker or Vagrant,'
echo 'the port number may be something else.'
echo 'If you are using Docker Machine,'
echo 'replace "localhost" with a numerical' 
echo 'IP address (probably 192.168.99.100).'

echo '-----------------------'
echo 'rails server -b 0.0.0.0'
rails server -b 0.0.0.0
```
* In Docker, enter the command "tmux" to begin tmux mode.  Press Ctrl-b and then "c" to create a second tmux window.  In one of your tmux screens, cd into your project's root directory, and enter the command "sh server.sh" to run your local server.  Use a browser in the host machine to see what your Rails app looks like.  Use the other tmux screen to continue entering commands.  Press Ctrl-b and then "p" to go to the previous tmux screen.  Press Ctrl-b and then "n" to go to the next tmux
screen.  To scroll within a tmux screen, press Ctrl-b and then your normal keys for paging up and paging down.  To exit scroll mode (which disables your ability to do anything other than scroll up or down), press Ctrl-c.   If you need to stop the server for any reason, just go to that tmux screen and press CTRL-c.  To close a tmux screen, go to that screen and enter "exit".
* I recommend always keeping a tmux window dedicated to the local server, because it shows you what a visitor to your site would see.  Always keep it running when you are working on your project unless you need to restart it.
* While the local server is running, open the web browser in your host environment and go to the appropriate URL.  You should see the Ruby on Rails default splash screen.  Keep this web browser tab open, and refresh it periodically to see the progress in this project.
* Create the file index.html in the public directory of your project with the following content:
```
<html>
	<head>
		<title>public/index.html</title>
	</head>
	<body>
		Welcome to public/index.html!
	</body>
</html>
```
* Refresh your web browser tab.  The Ruby on Rails splash screen should now be replaced with a plain page that reads "Welcome to public.html!"
* Enter the following commands:
```
git status # server.sh and public/index.html
git add .
git commit -m "Added server.sh"
```

### sandbox.sh
*  Create the file sandbox.sh with the following content:
```
#!/bin/bash

# This script starts the Rails sandbox.

rails console --sandbox
```
*  Enter the command "sh sandbox.sh" to start the Rails sandbox.  When you are finished trying it out, just enter "exit" in the Rails sandbox console to return to the normal Bash command line.
*  If you frequently use a Rails sandbox console, you should keep a tmux window dedicated to it.
*  Enter the following commands:
```
git status
git add .
git commit -m "Added sandbox.sh"
```
### git_check.sh
*  Create the file git_check.sh with the following content:
```
#!/bin/bash

sh build_fast.sh

echo '----------'
echo 'git status'
git status
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added git_check.sh"
```

### credentials.sh
* Create the file credentials.sh with the following content:
```
#!/bin/bash

# This script provides one-stop shopping for entering your credentials.
# Please run this script when you reset the Docker container.
# The "git commit" command will not work without your Git credentials.

# Output:
# First argument if it is not blank
# Second argument if first argument is blank
anti_blank () {
  if [ -z "$1" ]; then
    echo "$2"
  else
    echo "$1"
  fi
}

echo '**************************'
echo 'SETTING GITHUB CREDENTIALS'
EMAIL_DEF='you@example.com'

echo
echo "Default email address: ${EMAIL_DEF}"
echo
echo 'Enter your GitHub email address:'
read EMAIL_SEL
EMAIL=$(anti_blank $EMAIL_SEL $EMAIL_DEF)
echo

echo
echo '------------------------------'
echo "git config --global user.email"
echo "$EMAIL"
git config --global user.email "$EMAIL"

NAME_DEF='Your Name'
echo
echo "Default name: ${NAME_DEF}"
echo
echo 'Enter your GitHub name:'
read NAME_SEL

# NOTE: The double quotes are needed to avoid truncating the string
# at the space.
NAME=$(anti_blank "$NAME_SEL" "$NAME_DEF")

echo
echo '-----------------------------'
echo "git config --global user.name"
echo "$NAME"
git config --global user.name "$NAME"
echo
```
* Enter the command "sh credentials.sh" to run this script.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added credentials.sh"
```

### Wrapping Up
* Enter the command "git push origin 01-02-basic_scripts".
* Go to the GitHub repository and click on the "Compare and pull request" button for this 01-02-basic_scripts branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull origin master
```

### Conclusions
* Be sure to run the git_check.sh script before entering a "git commit" command.
* Keep the local web server running at all times.  Dedicate one entire tmux screen for doing this so that you can easily stop it and restart it if necessary.
