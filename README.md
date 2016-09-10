# Starting a New Ruby On Rails App From Scratch

## What's the Point?
* This document provides a step-by-step approach to creating a template Rails app for the GenericApp gem.  Having a standard operating procedure enables the quick creation of a comprehensive app without cutting corners on quality.
* The steps in this document can be adapted for improving the quality of a legacy Rails app.
* This is part of my [Ruby on Racetracks](http://www.rubyonracetracks.com/) system of shortcuts, automation, and cheat sheets.

## Prerequisites

### Rails Tutorial
* It is assumed that you have completed Michael Hartl's [Rails Tutorial](https://www.railstutorial.org/).
* Although this app looks similar to the Rails Tutorial Sample App, there are a number of differences.  This app is intended for use as a basic platform for other Rails apps.

### Docker
It is assumed that you are using my rbenv-general Docker image.  The link to the appropriate GitHub repository is at [Ruby on Racetracks](http://www.rubyonracetracks.com/).  (Use either the 32-bit version or the 64-bit version, whichever is compatible with your hardware.)  rbenv, and recent versions of Ruby, Rails, and other components come pre-installed.  You can be up and running with a fresh Ruby on Rails installation in minutes, not hours.

## Git Branches
*  Except when otherwise noted, each chapter has its own Git branch.  In some cases, commits are made to this branch at multiple times.  At the end of each chapter, the branch is merged into master.  In the next chapter, a new branch is created from master, and the process repeats.
*  The process of creating this app is split into smaller steps than Rails Tutorial is.  You should be able to complete a given chapter within a few minutes and with only a modest amount of effort.  Michael Hartl's steps are too large for me, and I easily get lost in the process.
