# Unit 4
## Chapter 4: Code Climate GPA Rating

### Code Climate Web Site
* Log in to the [Code Climate](https://codeclimate.com) web site.  If you have not already done so, you must first create an account.
* On your Code Climate dashboard page, click on "Add Open Source Repo" and submit the name of the GitHub repository you wish to have monitored.
* Download the codeclimate-config.patch file provided, and save it in your project's root directory.

### Code Climate Files
* Enter the following commands:
```
patch < codeclimate-config.patch
rm codeclimate-config.patch
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added CodeClimate configuration files"
git push origin master
```
* Code Climate now provides a GPA rating of your code quality and a badge denoting this.
