# Unit 1
## Chapter 1: Initial Commit

### A. Creating the New App

Open your Docker container from the appropriate rbenv-general Docker image (as discussed in the README.md file).  In the shell window, go to the /home/winner/shared directory.  To create a new app named after the current date, enter the following commands:
```
DATE=`date +%Y_%m_%d`
rails new generic_rails_$DATE
cd generic_rails_$DATE
```

### B. .gitignore file
Let's update the new project's .gitignore file.  Edit this file, make the following changes, and then save the file.
*  At the beginning of the .gitgnore file, enter the following lines:
```
########################
# BEGIN:initial contents
########################
```
*  At the end of the .gitignore file, enter the following lines:
```
#######################
# END: initial contents
#######################

.DS_Store # Prevents Mac users from accidentally adding .DS_Store

/spring/*.pid # Ignore Spring files.

```
### C. Gemfile
*  By default, your new Rails app uses SQLite as its database.  The production environment uses PostgreSQL, which is not specified in the original Gemfile.  Because Unit 1 focuses on the development environment, we'll leave the original contents of the Gemfile alone but label the contents for easy replacement later.
*  At the beginning of the Gemfile, enter the following lines:
```
########################
# BEGIN:initial contents
########################
```
*  At the end of the Gemfile file, enter the following lines:
```
#######################
# END: initial contents
#######################
```

### D. Setting Up Git Locally

*  Enter the following commands in your shell window (providing your GitHub email address and name, respectively):
```
  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"
```
* Please note that you must repeat the above step every time you enter a new development environment or reset your Docker container.  Until you do so, you are unable to git commit, and you will see an error message telling you to set these Git configurations when you attempt to do so.

### E. GitHub Repository
* In your web browser, go to [GitHub](https://github.com/) and create a new repository with the same name as that of this new 
Rails app.  Stick with the default options to avoid financial costs and merge conflicts.  You will see instructions for making the initial push from your local machine to the GitHub repository.
* In your shell window, cd into your project's root directory and enter the following commands:
```
git init
git status # Shows list of files in your project, starting with .gitignore
git add .
git commit -m "initial commit"
```
* Go to your generic_rails-* repository on GitHub and follow the instructions for pushing an existing repository from the command line.
* After you have pushed your repository, refresh the above browser window.  The instructions have been replaced with the source code of your new app.
