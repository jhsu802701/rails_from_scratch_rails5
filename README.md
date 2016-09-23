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
*  Except when otherwise noted, each chapter has its own Git branch.  In some cases, commits are made to this branch in the middle of the chapter.  At the end of each chapter, checks are done, and the branch is merged into master.  In the next chapter, a new branch is created from master, and the process repeats.
*  The process of creating this app is split into much smaller steps than Rails Tutorial is.  You should be able to complete a given chapter within a few minutes and with only a modest amount of effort.  If you encounter any surprises, it's much easier to troubleshoot (and learn) when the changes are very modest.  Michael Hartl's steps are too large for me, and I easily get lost in the process.

## Ruby and Gem Versions
*  The Ruby version (specified in the Gemfile) is the same one provided in the recommended rbenv-general Docker image.  This saves you from the time-consuming processes of installing the specified version of Ruby and installing the rails, nokogiri, and pg gems in each.
*  Many of the gem versions specified in this repository will be outdated by the time you read this.  


## Gem Version Updating Process
*  Edit the Gemfile to specify a new version of the gem.
*  Enter "bundle update; sh all.sh".
*  If all goes well, all tests pass.

## Things Worth Remembering
* Do NOT mix files and comments on the same line in .gitignore.  The filename/path MUST have its own dedicated line.
* If the "rails test" command is called from a script within a script (instead of being called directly or called through a script), some of the minitest-reporter options may not work, and this is why there is duplication between the build_fast.sh script and the all.sh script.
