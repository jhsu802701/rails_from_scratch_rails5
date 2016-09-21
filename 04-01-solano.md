# Unit 4
## Chapter 1: Solano CI

### Setting Up On the Solano CI Web Site
* Log in to the [Solano CI](https://ci.solanolabs.com/) web site.  If you haven't already done so, you need to create an account first.
* Go to your Accounts page.
* Enter your Rails app.

### solano.yml
* Create the file solano.yml with the following content (replacing the Ruby version if necesssary):
```
ruby_version: ruby-2.3.1
```
* Enter the command "sh git_check.sh"
* Enter the following commands:
```
git add .
git commit -m "Added solano.yml"
git push origin master
```

### Solano CI Test Results
* Pushing the code to GitHub automatically triggers Solano CI to run its tests.
* In a few moments, you will see the build test results.  Your app should be passing.
