# Unit 9
## Chapter 12: Users Lock

### New Branch
Enter the command "git checkout -b 09-12-users_lock".

### Gemfile
* Add the following lines to the end of the Gemfile:
```
group :testing do
  gem 'timecop' # Changes current time, needed for testing the lock duration
end
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added timecop gem"
```


### Integration Test
* Enter the command "rails generate integration_test users_lock".

### Routing

### Devise Configuration

### Wrapping Up
* Enter the command "git push origin 09-12-users_lock".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
