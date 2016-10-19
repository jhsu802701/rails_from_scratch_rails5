# Unit 6
## Chapter 2: Creating Models

In this chapter, you will create the user and admin models.

### New Branch
Enter the command "git checkout -b 06-02-create_models".

### Adding Models
* Create the user model by entering the following command:
```
rails generate devise USER
```
* Create the admin model by entering the following command:
```
rails generate devise ADMIN
```
* Note that the db/migrate directory now contains scripts that create the admin and user tables in the database and sets the parameters.
* Enter the command "rails db:migrate" to implement these database migrations. Until you do so, the admin and user tables will not exist.
* Enter the command "sh git_check.sh". You'll see some RuboCop offenses and a long list of failing tests.
* Refresh your local view of your app in your browser.  You will see error messages.

### Fixing The App
* Replace the test/fixtures/admins.yml and test/fixtures/users.yml files with the following content:
```
# Read about fixtures at
# http://api.rubyonrails.org/classes/ActiveRecord/FixtureSet.html

# These fixtures are disabled initially.
# The original fixtures created by the Devise gem cause all tests to fail.
```
* Enter the command "rails test".  All of your tests should now pass.
* Refresh your local view of your app in your browser.  Your app should now work again.
* Add new lines consisting of just "#" prior to the class definitions in app/models/admins.rb and app/models/users.rb files.
* Enter the command "sh git_check.sh".  Your app should now be in full compliance again.
* Enter the following commands:
```
git add .
git commit -m "Added user and admin models"
```

### Wrapping Up
* Enter the command "git push origin 06-02-create_models".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
