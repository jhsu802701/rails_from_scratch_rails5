# Chapter 4: Resetting Your Development Environment

It's a good idea to reset your development environment occasionally.  Because we are not editing the project's source code in this chapter, there is no need to create a new branch.

A. Resetting the Docker Container

*  If you still have your development environment open, stop all processes in your tmux screens.  Enter "exit" to close each tmux screen.  Enter "exit" to leave the Docker container and return to your host machine.
*  Enter "sh reset.sh" to run the Docker container reset script.  In a few seconds, you will be in a fresh development environment, with all changes made since the previous reset undone.
*  Note that the contents of the "shared" directory are preserved, because they were on your host system.

B. Deleting and Retrieving the Source Code.

*  Since you just pushed the previous chapter's changes to GitHub, you are now free to delete your project from your local machine.
*  After you have deleted your project from the local machine, you can retrieve your project from GitHub.  
*  Go to your GitHub repository for your project, and click on the "Clone or download" button to look up your project's GitHub URL.
