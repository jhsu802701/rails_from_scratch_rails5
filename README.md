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

## Things Worth Remembering
* Do NOT mix files and comments on the same line in .gitignore.  The filename/path MUST have its own dedicated line.
* When you run the "rails test" command from a script within a script, some of the minitest-reporter features (like the green/red output) won't work, and the AwesomeReporter option will throw an error message and prevent the tests from running.  This is why the all.sh script must call the "rails test" command directly instead of calling it indirectly through the build_fast.sh script.
