# Unit 10
## Chapter 1: Showing Users

In this chapter, you will provide user profiles.  The only people who should be allowed to view a user profile are that specific user and the admins.

### New Branch
Enter the command "git checkout -b 10-01-users_show".

### User Controller
* Enter the command "rails generate controller Users new".
* Enter the command "rm app/helpers/users_helper.rb".

### Wrapping Up
* Enter the command "git push origin 10-01-users_show".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
