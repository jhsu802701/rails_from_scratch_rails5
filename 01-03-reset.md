# Unit 1
## Chapter 3: Resetting Your Development Environment

It's a good idea to reset your development environment occasionally.  Because we are not editing the project's source code in this chapter, there is no need to create a new branch.

### A. What's the Point?

*  Resetting your development environment helps you to prevent the infamous "but it works on my machine" problem, which I am calling a DEPENDENCY MISMANAGEMENT problem.  This can be a major time liability in the production environment, for new members of your team, or when you need to set up a project on a different computer.  The dependency mismanagement problem is frustrating and embarassing.  It stops you in your tracks, and it reflects poorly on your knowledge of what it really takes to make your project work.
*  The combination of the build_fast.sh script in your project and the scripts used in creating the project's Docker image show EXACTLY what it takes to make your project work.  In other words, everyone on your team has access to a giant cheat sheet.
*  Resetting your development environment means that you are addressing one of Joel Spolsky's criteria for a well-run software project - the 1-step build process.

### B. Why now?

In the previous chapter, we made changes to the Gemfile.  This is a time when the risk of creating a dependency mismanagement problem is greater than usual.  For example, if you remove the sqlite gem from the Gemfile but forget to enter "bundle install" before entering "bundle exec rake test", your tests will still pass even though an essential part of the Gemfile has been removed.

### C. Resetting the Docker Container

*  If you still have your development environment open, stop all processes in your tmux screens.  Enter "exit" to close each tmux screen.  Enter "exit" to leave the Docker container and return to your host machine.
*  Enter "sh reset.sh" to run the Docker container reset script.  In a few seconds, you will be in a fresh development environment, with all changes made since the previous reset undone.  As long as you keep the same version of this Docker image, you will have the exact same conditions every time you run the associated reset.sh script.
*  Note that the contents of the "shared" directory are preserved, because they were on your host system.

### D. Deleting and Retrieving the Source Code.

*  Since you just pushed the previous chapter's changes to GitHub, you are now free to delete your project from your local machine.
*  After you have deleted your project from the local machine, you can retrieve your project from GitHub.  
*  Go to your GitHub repository for your project, and click on the "Clone or download" button to look up your project's GitHub URL.
*  In the shared directory, enter the command "git clone <GitHub URL>".  This downloads your code.

### E. Setting Up Your Project
*  In your project's root directory, enter the command "sh build_fast.sh" to run the script that builds and tests your project.  This takes care of "bundle install", database migrations, and testing process.  If the Docker image came with the versions of Ruby, Rails, pg, and Nokogiri that are used by the project, then the build_fast.sh script will only need a few minutes to execute.  Since the rbenv-general Docker image is set up for the Rails Tutorial Sample App, you should create a customized Docker image for your project.
*  If your project passed all tests, and the outcome of running the build_fast.sh script was as expected, then this means that you have covered all bases through the Docker image build scripts and the build_fast.sh script.
*  If your project didn't behave as expected, it's time to figure out what's missing, and you should be thankful that you spotted the dependency mismanagement problem early on.

### F. Configurations
*  Before you can successfully use the "git commit" command, you need to configure your Git with your GitHub credentials again with the following commands: 
```
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```
*  Before you can successfully use the heroku.sh script again, you'll need to follow the steps in this script's comments to get back in business.  You must configure the local environment and set the name of the Heroku repository.

### G. Conclusions

*  If all goes well, it only takes a few minutes to check for dependency mismanagement problems.
*  If your project doesn't behave as expected when you run the build_fast.sh script, you now can address your dependency mismanagement problem while it's still small.  Not having a Docker image with the right elements for the project and not having a 1-step build script means that it's much more difficult and time-consuming to check for dependency mismanagement problems.  This encourages people to neglect this responsibility and allow the hidden problems to fester.  This means that when the dependency mismanagement problems show up later, the troubleshooting process is MUCH, MUCH more difficult.  In other words, individuals and teams who neglect dependency mismanagement checking are in a situation analogous to those of students who wait until the last minute to start work on a term paper.
