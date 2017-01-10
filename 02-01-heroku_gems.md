# Unit 2
# Chapter 1: Gems for Heroku

In this chapter, you will revise the Gemfile.  The main change is replacing the sqlite3 (SQLite) gem with pg (PostgreSQL) gem for the production environment.  If you do not make this change, you will not be able to deploy your app to Heroku.  Additionally, the nokogiri, pg, and rails gems may be downgraded to the versions used in the current Rails Tutorial Sample App.  The specific versions of nokogiri, pg, and rails that are used in this tutorial app are pre-installed in my rbenv-general Docker image.  This pre-installation is necessary to minimize the amount of time needed to wait for these large gems to be installed.  (REMEMBER: While the "bundle install" command will automatically install the correct version of each gem needed, this process is much faster if the correct versions of the largest gems were pre-installed with the "gem install" command.)

### New Branch
Enter the command "git checkout -b 02-01-heroku_gems".

### Removing sqlite3
* Heroku will not accept your attempt to deploy if your master branch INCLUDES the use of the pg (SQlite) gem in production.  As you can see in the original Gemfile, the sqlite gem is used in all environments by default.  To disable sqlite in production, limit its use to the development and testing environments only by replacing the line beginning with "# Use sqlite3" and the line consting of "gem 'sqlite'" with the following:
```
# BEGIN: SQLite
# NOTE: This section is automatically deleted by the pg_setup.rb script
group :development, :test do
  gem 'sqlite3'
end
# END: SQLite
```

### Adding pg
* Heroku will not accept your attempt to deploy if your master branch does not specify the use of the pg (PostgreSQL) gem in production.  (As you can see in the original Gemfile, the pg gem is not specified.)
* Remove the line beginning with "# Bundle edge Rails instead" and the line beginning with "gem 'rails'".  Replace these lines with the following:
```
##############################################
# BEGIN: gems that take a long time to install
##############################################
# Please pre-install the proper versions in the Docker image.
gem 'nokogiri'
gem 'pg'
gem 'rails'
############################################
# END: gems that take a long time to install
############################################

```
* The nokogiri gem is a dependency of minitest.
* The pg gem (PostgreSQL) is needed in the production environment for Heroku, because it does NOT offer SQLite.  In the production environment, PostgreSQL is greatly preferred over SQLite.  While it's usually best to use the same database in the development and production environments, the use of SQLite in development and PostgreSQL in production (for Heroku) is usually used in tutorials.  If your production environment is not Heroku, it's best to use PostgreSQL in the development and testing environments and skip the use of SQLite completely.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be offenses.
* Enter the command "git diff Gemfile.lock".  Note that no gem has been upgraded or downgraded to a different version.
* Enter the following commands: 
```
git add .
git commit -m "Updated Gemfile for Heroku"
```

### Pinning Gems
* In this section, you will pin the version numbers of nokogiri, pg, and rails currently used in this app.
* In the Gemfile.lock file, look up the specific version number of nokogiri.  The specific version number will NOT be modified by "~>" or ">=".
* In the Gemfile, specify the current version number for nokogiri.  For example, if the current version of nokogiri is 1.7.0.1, Replace "gem 'nokogiri'" with "gem 'nokogiri', '1.7.0.1'".
* Repeat the above steps for pg and rails.
* Enter the following commands:
```
git add .
git commit -m "Pinned versions of nokogiri, pg, and rails"
```

### Downgrading Gems
* In this section, you will pin the version numbers of nokogiri, pg, and rails to those used in the Rails Tutorial Sample App.
* Go to the source code for the current Rails Tutorial Sample App.  In the Gemfile.lock file, look up the specific version number of nokogiri.
* In the Gemfile, specify the version of nokogiri used in the Rails Tutorial Sample App.  (Depending on how current the Rails Tutorial Sample App is, you may or may not need to downgrade the version number.)
* Enter the command "bundle update nokogiri".
* Repeat the above steps for the pg and rails gems.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the command "git diff Gemfile.lock" to see the changes.  Note that if rails is downgraded, some of its dependencies may be downgraded as well.
* Enter the following commands:
```
git add .
git commit -m "Downgraded nokogiri, pg, and rails to the versions in Rails Tutorial"
git push origin 02-01-heroku_gems
```

### Reset
* Begin following the steps in Unit 1 Chapter 3 for resetting your development environment and downloading the source code.
* After you have downloaded the source code, go to the first tmux screen, cd into the root directory of your project, and enter the command "git checkout 02-01-heroku_gems".  This switches you from the master branch to the 02-01-heroku_gems branch and downloads the source code revisions of this alternate branch.
* Enter your app's root directory, and enter the command "sh credentials.sh; sh all.sh; sh server.sh". (Remember that the test_code.sh script and tools like RuboCop and Rails Best Practices were not yet in place back in Unit 1 Chapter 3.)
* pen a second tmux window and go to your app's root directory.  You will use this tmux window for entering commands.
* Once the local server is running, open your web browser and view your project.
* If your project passed all tests, you are able to view your local app in the browser, and the outcome of running the all.sh script was as expected, then this means that you have covered all bases through the Docker image build scripts and the build_fast.sh script.

### Wrapping Up 
* Go to the GitHub repository and click on the "Compare and pull request" button for this 02-01-heroku_gems branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull origin master
```
