# Unit 1
## Chapter 3: Resetting Your Development Environment

It's a good idea to reset your development environment occasionally.  Because you are not editing the project's source code in this chapter, there is no need to create a new branch.

### What's the Point?

*  Resetting your development environment helps you to prevent the infamous "but it works on my machine" problem, which I am calling a DEPENDENCY MISMANAGEMENT problem.  This can be a major time liability in the production environment, for new members of your team, or when you need to set up a project on a different computer.  The dependency mismanagement problem is frustrating and embarassing.  It stops you in your tracks, and it reflects poorly on your knowledge of what it really takes to make your project work.
*  The combination of the build_fast.sh script in your project and the scripts used in creating the project's Docker image show EXACTLY what it takes to make your project work.  In other words, everyone on your team has access to a giant cheat sheet.
*  Resetting your development environment means that you are addressing one of Joel Spolsky's criteria for a well-run software project - the 1-step build process.

### Factors that Increase the Risk Of Dependency Mismanagement
* When you install a gem with the "gem install" command (instead of putting it in the Gemfile and entering "bundle install" or "bundle update"), your risk of dependency mismanagement increases.  This is the case, because entering the "gem install" command to install a gem does not add it to the source code.  In contrast, the Gemfile is part of the source code.
* If you edit the Gemfile (ESPECIALLY if you delete gems), your risk of dependency mismanagement increases.  If you forget to enter "bundle install" or "bundle update" immediately afterwards, then your source code has changed, but your development environment has not.  This can lead to a false sense of security about this change.

### Resetting the Docker Container

*  If you still have your development environment open, stop all processes in your tmux screens.  Enter "exit" to close each tmux screen.  Enter "exit" to leave the Docker container.
*  When you are out of the Docker container, enter "sh reset.sh" to run the Docker container reset script.  In a few seconds, you will be in a fresh development environment, with all changes made since the previous reset undone.  As long as you keep the same version of this Docker image, you will have the exact same conditions every time you run the associated reset.sh script.
*  Note that the contents of the "shared" directory are preserved, because they were on your host system.

### Deleting and Retrieving the Source Code

*  Since you just pushed the previous chapter's changes to GitHub, you are now free to delete your project from your local machine.
*  After you have deleted your project from the local machine, you can retrieve your project from GitHub.  
*  Go to your GitHub repository for your project, and click on the "Clone or download" button to look up your project's GitHub URL.
*  In the /home/winner/shared directory in your Docker container, enter the command "git clone (GitHub URL)".  This downloads your code.

### Setting Up Your Project

*  In the Docker container, start tmux.
*  In your project's root directory, enter the command "sh build_fast.sh; sh server.sh" to execute the build process and start the local server.  This takes care of "bundle install", database migrations, testing process, and starting the local server so that you can view your app on your host system's browser.  If the Docker image came with the specific versions of Ruby, Rails, pg, and Nokogiri that are used by the project, then the build_fast.sh script will only need a few minutes to execute.  Since the rbenv-general Docker image is set up for the Rails Tutorial Sample App, you should create a customized Docker image for your project.
*  Once the local server is running, open your web browser and view your project.
*  If your project passed all tests, you are able to view your local app in the browser, and the outcome of running the build_fast.sh script was as expected, then this means that you have covered all bases through the Docker image build scripts and the build_fast.sh script.
*  If your project didn't behave as expected, it's time to figure out what's missing, and you should be thankful that you spotted the dependency mismanagement problem early on.
*  Because your local server is occupying the first tmux screen, you need to start a second tmux screen to continue entering commands.

### Reconfiguring Git

Before you can successfully use the "git commit" command, you need to reconfigure your Git with your GitHub credentials with the following commands (and providing your GitHub email address and name, respectively): 
```
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

### Conclusions

*  If all goes well, it only takes a few minutes to check for dependency mismanagement problems.
*  If your project doesn't behave as expected when you run the build_fast.sh script, you now can address your dependency mismanagement problem while it's still small.  Not having a Docker image with the right elements for the project and not having a 1-step build script means that it's much more difficult and time-consuming to check for dependency mismanagement problems.  This encourages people to neglect this responsibility and allow any hidden problems to fester.  This means that when the dependency mismanagement problems show up later, the troubleshooting process is MUCH, MUCH more difficult.  In other words, individuals and teams who neglect dependency mismanagement checking are in a situation analogous to those of students who wait until the last minute to start work on a term paper.
