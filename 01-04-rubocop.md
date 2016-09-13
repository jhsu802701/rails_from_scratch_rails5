# Unit 1

## Chapter 4: RuboCop

RuboCop is a Ruby code analyzer that points out violations of the Ruby Style Guide.  Violations don't cause the code to fail but can slow down expert Rubyists when they read and analyze the code.  It's best to add RuboCop very early in a project, because it gets increasingly difficult to introduce later on.

### New Branch

Enter the command "git checkout -b 01-04-rubocop".

### Gemfile

* Add the following lines to the end of the Gemfile:
```

# BEGIN: gems used in test_code.sh script

gem 'rubocop', '0.42.0', require: false # Code style checking tool; not recommended for legacy apps
```
* Enter the command "bundle install; rubocop".  This installs and runs RuboCop.  You can see why it's best to add Rubocop early on and why I don't recommend adding it to a legacy app.  Even at this early stage in the project, I found 48 violations.
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Installed Rubocop"
```
### .rubocop.yml
* Create the file .rubocop.yml with the following content:
```
AllCops:
  Exclude:
    - db/schema.rb

Metrics/LineLength:
  Exclude:
    - bin/spring
    - config/application.rb
    - config/environments/development.rb
    - config/environments/production.rb
    - config/initializers/assets.rb
    - config/initializers/backtrace_silencers.rb
    - config/initializers/new_framework_defaults.rb
    - config/initializers/session_store.rb
    - config/initializers/wrap_parameters.rb
    - config/environments/production.rb
    - db/seeds.rb
    - Gemfile
    - Rakefile
    - test/test_helper.rb
```
* The .rubocop.yml file tells RuboCop which violations to ignore in specific files.  Some violations are impossible to avoid or are not worth fixing.  For example, db/schema.rb is automatically generated every time you migrate the database.
* Enter the command "rubocop" to see how many violations remain.  This .rubocop.yml file reduced the number of violations from 48 to 14.  These remaining violations are relatively easy to fix.
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Added .rubocop.yml"
```
### RuboCop Compliance
*  In the config/puma.rb file, replace the double-quotes cited by RuboCop with single-quotes.  Enter the command "rubocop" again.  10 violations remain.
*  In the config/environments/production.rb file, replace the double-quotes cited by Rubocop, and get rid of the extra spaces it cited.
*  In the app/helpers/application_helper.rb, app/controllers/application_controller.rb, app/mailers/application_mailer.rb, and app/models/application_record.rb files, add a "#" to the lines directly above the "class" statements cited.
*  The Gemfile has two blank lines following the line "source 'https://rubygems.org'".  Remove one of these two blank lines.
*  In the test/test_helper.rb line, switch from the compact style to the nested style as directed by Rubocop.  The resulting file should be:
```
ENV['RAILS_ENV'] ||= 'test'
require File.expand_path('../../config/environment', __FILE__)
require 'rails/test_help'

class ActiveSupport
  class TestCase
    # Setup all fixtures in test/fixtures/*.yml for all tests in alphabetical order.
    fixtures :all

    # Add more helper methods to be used by all tests here...
  end
end
```
* Enter the command "rubocop" again.  There should be no violations.
* Enter the command "sh build_fast.sh".  Everything should proceed as expected.
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "RuboCop compliant"
```
### git_check.sh
* Add the following lines after the line "sh build_fast.sh":
```

echo '-------'
echo 'rubocop'
rubocop
```
* Now you can easily check for RuboCop violations prior to executing the git add and git commit commands.  Enter the command "sh git_check.sh".
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Added rubocop command to git_check.sh"
```
### Wrapping Up
*  Enter the command "sh git_check.sh". There should be no new files or changes left to add.
*  Enter the command "git push origin 01-04-basic_rubocop".
*  Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
*  Accept this pull request to merge it with the master branch, but do NOT delete this branch.
*  Enter the following commands:
```
git checkout master
git pull
```
