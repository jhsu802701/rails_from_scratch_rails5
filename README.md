# Starting a New Ruby On Rails App From Scratch

## What's the Point?
*  This document provides a step-by-step approach to creating a template Rails app for the GenericApp gem.  Having a standard operating procedure enables the quick creation of a comprehensive app without cutting corners on quality.
*  The steps in this document can be adapted for improving the quality of a legacy Rails app.
*  This is part of my [Ruby on Racetracks](http://www.rubyonracetracks.com/) system of shortcuts, automation, and cheat sheets.

## Prerequisites

### Rails Tutorial
*  It is assumed that you have completed Michael Hartl's [Rails Tutorial](https://www.railstutorial.org/).
*  Although this app looks similar to the Rails Tutorial Sample App, there are a number of differences.  This app is intended for use as a basic platform for other Rails apps.

### Docker
It is assumed that you are using my rbenv-general Docker image.  The link to the appropriate GitHub repository is at [Ruby on Racetracks](http://www.rubyonracetracks.com/).  (Use either the 32-bit version or the 64-bit version, whichever is compatible with your hardware.)  rbenv, and recent versions of Ruby, Rails, and other components come pre-installed.  You can be up and running with a fresh Ruby on Rails installation in minutes, not hours.

## Git Branches
The process of creating this app is split into much smaller steps than Rails Tutorial is.  You should be able to complete a given chapter within a few minutes and with only a modest amount of effort.  If you encounter any surprises, it's much easier to troubleshoot (and learn) when the changes are very modest.  Michael Hartl's steps are too large for me, and I easily get lost in the process.

## Ruby and Gem Versions
Because this app is a platform for other apps and not intended as a stand-alone app, the Ruby version is not specified, and the versions of each gem in the Gemfile are not specified.

## Gem Version Updating Process
*  Edit the Gemfile to specify a new version of the gem.
*  Enter "bundle update; sh all.sh".
*  If all goes well, all tests pass.

## Docker Container Reset Procedure
* If necessary, commit and push your source code to GitHub.  (This can be done in the master branch or an alternate branch, whichever is appropriate.)
* Exit the Docker container, and enter "sh reset.sh" to run the Docker container reset script.
* Delete your project's source code from your local machine.
* In the /home/winner/shared directory, enter the command "git clone (GitHub URL)".
* Enter tmux mode.  In the first tmux screen, enter your project's root directory.  If necessary, enter "git checkout (name of branch)" to switch to the appropriate branch.
* Enter the command "sh all.sh; sh server.sh".
* Once the local server is running, open your web browser and view your project.
* If the setup process and the local appearance and behavior of your app are as expected, then you have no dependency mismanagement problems.  If your project didn't behave as expected, it's time to figure out what's missing, and you should be thankful that you spotted the dependency mismanagement problem early on.
* Before you can successfully use the "git commit" command, you need to reconfigure your Git with your GitHub credentials with the following commands (and providing your GitHub email address and name, respectively):
```
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```
* Follow the instructions in the comments in heroku.sh to restore your ability to push to Heroku.  Enter the command "sh heroku.sh" to confirm that you have completed this step.

## Things Worth Remembering
* Do NOT mix files and comments on the same line in .gitignore.  The filename/path MUST have its own dedicated line.
* When you run the "rails test" command from a script within a script, some of the minitest-reporter features (like the green/red output) won't work, and the AwesomeReporter option will throw an error message and prevent the tests from running.  This is why the all.sh script must call the "rails test" command directly instead of calling it indirectly through the build_fast.sh script.
