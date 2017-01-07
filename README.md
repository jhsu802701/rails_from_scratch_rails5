# Starting a New Ruby On Rails App From Scratch

## What's the Point?
This is discussed on the [Ruby on Racetracks](http://www.rubyonracetracks.com/rails_from_scratch.html) web site.

## Prerequisites

### Rails Tutorial
*  It is assumed that you have completed Michael Hartl's [Rails Tutorial](https://www.railstutorial.org/).
*  Although this app looks similar to the Rails Tutorial Sample App, there are a number of differences.  This app is intended for use as a basic platform for other Rails apps.

### Docker
It is assumed that you are using my rbenv-general Docker image.  The link to the appropriate GitHub repository is at [Ruby on Racetracks](http://www.rubyonracetracks.com/).  (Use either the 32-bit version or the 64-bit version, whichever is compatible with your hardware.)  rbenv, and recent versions of Ruby, Rails, and other components come pre-installed.  You can be up and running with a fresh Ruby on Rails installation in minutes, not hours.

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
* While you wait for the build process to complete, start a second tmux window.  In this second window, enter the command "sh credentials.sh" to enter your GitHub and Heroku credentials.  The "git commit" command will not work without your Git credentials, and you cannot push to Heroku without your Heroku credentails.
* Once the local server is running, open your web browser and view your project.
* If the setup process and the local appearance and behavior of your app are as expected, then you have no dependency mismanagement problems.  If your project didn't behave as expected, it's time to figure out what's missing, and you should be thankful that you spotted the dependency mismanagement problem early on.

## Things Worth Remembering
* Do NOT mix files and comments on the same line in .gitignore.  The filename/path MUST have its own dedicated line.
* Attempts to log the screen output of "rails test" AND use the minitest-reporters features resulted in the message "uninitialized constant Minitest::Reporters::ANSI::Code::ENDCODE (NameError)".  As a result, the screen output of the all.sh script is abbreviated for better readability.

## Excluded Features
* Guard is excluded.  Reasons for this omission are explained in the Quick Testing Chapter (Unit 3 Chapter 2).
* SimpleCov and the Code Climate Test Coverage badge are excluded.  SimpleCov sometimes showed models to be uncovered in spite of the inclusion of model tests.  Additionally, I encountered divergences between the local environment's coverage results and Code Climate's coverage results.
