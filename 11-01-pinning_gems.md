# Unit 11
## Chapter 1: Pinning Gem Versions

In this chapter, you will pin the version numbers of all of the gems in the Gemfile, but you will NOT upgrade any gems just yet.

### What's the Point?
* Not all of the gems used by your app are listed in the Gemfile.  A look at the Gemfile.lock file shows that your app uses many gems beyond those in the Gemfile.
* The process of upgrading gems should be done in a series of small steps instead of one big step.  If tests fail, it's easy to identify the culprits when you upgrade the gems in small steps.

### New Branch
Enter the command "git checkout -b 11-01-pinning_gems".

### Gemfile
* For each gem listed in the Gemfile, enter the command "gem list (gem name)".
  * If you see just one gem version listed, this is the version to specify.  Remember, this chapter is about pinning the gem versions.  (The upgrading comes later.)
  * If you see more than one gem version listed, search through the Gemfile.lock file for the specific version used in the app.  This is the version to specify.
* When you are finished, enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Pinned all gems listed in the Gemfile"
```

### Wrapping Up
* Enter the command "git push origin 11-01-pinning_gems".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
