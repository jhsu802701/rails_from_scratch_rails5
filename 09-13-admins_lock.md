# Unit 9
## Chapter 13: Admins Lock
In this chapter, you will configure the app to lock admins after an excessive number of unsuccessful logins, just as you already did for users in the previous chapter.

### New Branch
Enter the command "git checkout -b 09-13-admins_lock".

### Integration Test
* Enter the command "rails generate integration_test admins_lock".
* Replace the contents of test/integration/admins_lock_test.rb with the following code:
```
```
### Routing

### app/views/users/unlocks/new.html.erb



### Wrapping Up
* Enter the command "git push origin 09-13-admins_lock".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
