# Unit 1
## Chapter 5: Other Metrics
In the previous chapter, you added the RuboCop tool.  In this chapter, you will add additional code analyzers.  As was the case with RuboCop, violations don't necessarily mean failing tests, but they can slow down expert Rubyists when they read and analyze the code.  Additionally, some of these tools can flag outdated and insecure gems.

### New Branch
Enter the command "git checkout -b 01-05-other_metrics".

### Gemfile
* Edit the end of the Gemfile so that it looks like this:
```
...
###################
# END: initial gems
###################

# BEGIN: gems used in test_code.sh script
group :development, :testing do
  gem 'bundler-audit' # Checks for vulnerable versions of gems
  gem 'brakeman' # Checks for security vulnerabilities
  gem 'sandi_meter' # Checks for compliance with Sandi Metz' four rules
  gem 'rubocop' # Checks for violations of the Ruby Style Guide, not recommended for legacy apps
  gem 'rails_best_practices' # Checks the quality of Rails code, not recommended for legacy apps
  gem 'gemsurance' # Checks for outdated and insecure gems
end
# END: gems used in test_code.sh script
```
* Enter the command "sh git_check.sh" to install the gems.
* Enter the following commands:
```
git add .
git commit -m "Installed additional code analysis gems" 
```
### test_code.sh
* Create the file test_code.sh with the following content:
```
#!/bin/bash

# This script runs the app through code metrics.
# Violations will not stop the app from passing but will be flagged here.

echo '--------------------------'
echo 'bundle install > /dev/null'
bundle install > /dev/null

# -A: runs all checks
# -q: output the report only; suppress information warnings
# -w2: level 2 warnings (medium and high only)
echo '----------------'
echo 'brakeman -Aq -w2'
brakeman -Aq -w2

echo '-----------'
echo 'sandi_meter'
sandi_meter

# Update the local ruby-advisory-db advisory database
echo '-------------------'
echo 'bundle-audit update'
bundle-audit update

# Audit the gems listed in Gemfile.lock
echo '------------'
echo 'bundle-audit'
bundle-audit

echo '----------'
echo 'rubocop -D'
rubocop -D

echo '----------------------'
echo 'rails_best_practices .'
rails_best_practices .

echo '----------------------------------------------'
echo 'gemsurance --output log/gemsurance_report.html'
gemsurance --output log/gemsurance_report.html
echo 'The Gemsurance Report is in log/gemsurance_report.html .'
```
* The Gemsurance Report shows which gems are up to date, which are out of date, and which have known security issues and thus more urgently need to be updated.
* In the .rubocop.yml file, add the tmp/vulnerabilities/lib/* files, tmp/vulnerabilities/spec/* files, and tmp/vulnerabilities/Rakefile to the list of AllCops exclusions.  (These files are automatically generated when you run the test_code.sh script.)  The .rubucop.yml file should look like:
```
AllCops:
  Exclude:
    - db/migrate/*
    - db/schema.rb
    - tmp/vulnerabilities/lib/*
    - tmp/vulnerabilities/spec/*
    - tmp/vulnerabilities/Rakefile
    
Metrics/LineLength:
. . .
```
* Enter the command "sh test_code.sh" to run this script.
* Enter the command "sh git_check.sh; rubocop; rails_best_practices".  The file gemsurance_report.html should be NOT listed.
* Enter the following commands:
```
git add .
git commit -m "Added test_code.sh; updated .gitignore and .rubocop.yml" 
```

### git_check.sh
* In the git_check.sh file, replace the entire contents of git_check.sh with the following:
```
#!/bin/bash

sh build_fast.sh

echo '----------------'
echo 'brakeman -Aq -w2'
brakeman -Aq -w2

echo '----------'
echo 'rubocop -D'
rubocop -D

echo '----------------------'
echo 'rails_best_practices .'
rails_best_practices .

echo '----------'
echo 'git status'
git status
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Updated git_check.sh" 
```

### all.sh
* Create the file all.sh with the following content:
```
#!/bin/bash

clear

sh build_fast.sh

FILE_LOG_TEST_CODE='log/all-test_code.log'

echo '-------------------------------------'
echo "sh test_code.sh > $FILE_LOG_TEST_CODE"
sh test_code.sh > $FILE_LOG_TEST_CODE
echo 'The Gemsurance Report is in gemsurance_report.html in the root directory.'
```
* Run this script by entering "sh all.sh".
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added all.sh"
```

### Wrapping Up
* Enter the command "git push origin 01-05-other_metrics".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
```

### Conclusion
Be sure to run the RuboCop, Rails Best Practices, and Brakeman tools before entering a "git commit" command.  These steps are covered in the git_check.sh script.  Fix all violations prior to entering the "git add" and "git commit" commands.
