# Unit 6
## Chapter 1: Creating Admin and User Models

In this chapter, you will create admin and user models with the Devise gem.  While Rails Tutorial shows how to create an authentication system, the Devise gem has much more credibility.  The purpose of the roll-your-own authentication system in the Rails Tutorial Sample App is to illustrate how authentication works, not to provide the best authentication system.

### New Branch
Start a new branch by entering the command "git checkout -b 06-01-create_models".

### Gemfile
* Add the following line to the end of your Gemfile:
```
gem 'devise' # Provides admin/user authentication
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Installed Devise gem"
```

### Configuring Devise
* Enter the command "rails generate devise:install".
* Create admin authentication controllers.  Enter the command "rails generate devise:controllers admins".  This creates admin authentication controllers that will be needed later.
* Create admin authentication pages.  Enter the command "rails generate devise:views admins".  This creates admin authentication pages that you will use later.
* Create user authentication controllers.  Enter the command "rails generate devise:controllers users".  This creates user authentication controllers that will be needed later.
* Create user authentication pages.  Enter the command "rails generate devise:views users".  This creates user authentication pages that you will use later.
* Edit the files config/environments/development.rb AND config/environments/test.rb so that they look like this:

```
(original content here)
...

  # From https://github.com/plataformatec/devise
  # Needed to avoid the error message:
  # ActionView::Template::Error: Missing host to link to!
  config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
end
```
* In the .rubocop.yml file, add config/initializers/devise.rb to the list of files exempt from the Metrics/LineLength cop.
* In the .rubocop.yml file, add "app/controllers/admins/\*" and "app/controllers/users/\*" to the list of files excluded from the Style/ClassAndModuleChildren cop.
* Add the following lines to the end of .rubocop.yml:
```
Style/CommentIndentation:
  Exclude:
    - app/controllers/admins/*
    - app/controllers/users/*
```

* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Configured Devise"
```
### Creating the Admin and User Models
* Create the user model by entering the following command:
```
rails generate devise USER
```
* Create the admin model by entering the following command:
```
rails generate devise ADMIN
```
* Note that the db/migrate directory now contains scripts that create the admin and user tables in the database and sets the parameters.
* Enter the command "rails db:migrate" to implement these database migrations.  Until you do so, the admin and user tables will not exist.
* Enter the command "sh git_check.sh".  You'll see some RuboCop offenses.
* Add new lines consisting of just "#" prior to the class definitions in app/models/admins.rb and app/models/users.rb files.
* Replace the test/fixtures/admins.yml and test/fixtures/users.yml files with the following content:
```
# Read about fixtures at
# http://api.rubyonrails.org/classes/ActiveRecord/FixtureSet.html

# These fixtures are disabled initially.
# The original fixtures created by the Devise gem cause all tests to fail.
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added the admin and user models"
git push origin 06-01-create_models
```

### Wrapping Up
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
