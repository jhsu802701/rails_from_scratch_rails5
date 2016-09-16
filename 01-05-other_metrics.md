# Unit 1
## Chapter 5: Other Metrics
In the previous chapter, you added the RuboCop tool.  In this chapter, you will add additional code analyzers.  As was the case with RuboCop, violations don't necessarily mean failing tests, but they can slow down expert Rubyists when they read and analyze the code.  Additionally, some of these tools can flag outdated and insecure gems.

### New Branch
Enter the command "git checkout -b 01-05-other_metrics".

### Gemfile
* Remove the line beginning with "gem 'rubocop'" from the Gemfile.  Replace this line with the following content:
```
group :development, :testing do
  gem 'bundler-audit', '0.5.0' # Checks for vulnerable versions of gems
  gem 'brakeman', '3.4.0' # Checks for security vulnerabilities
  gem 'sandi_meter', '1.2.0' # Checks for compliance with Sandi Metz' four rules
  gem 'rubocop', '0.42.0' # Checks for violations of the Ruby Style Guide, not recommended for legacy apps
  gem 'rails_best_practices', '1.17.0' # Checks the quality of Rails code, not recommended for legacy apps
  gem 'gemsurance', '0.6.0' # Checks for outdated and insecure gems
end
# END: gems used in test_code.sh script
```
* Enter the command "bundle install" to install the gems.
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Installed additional code analysis gems" 
```
### test_code.sh
* Create the file test_code.sh with the following content:
```
#!/bin/bash

echo '--------------'
echo 'bundle install'
bundle install

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

# -A: runs all checks
# -q: output the report only; suppress information warnings
# -w2: level 2 warnings (medium and high only)
echo '----------------'
echo 'brakeman -Aq -w2'
brakeman -Aq -w2

echo '-------'
echo 'rubocop'
rubocop

echo '----------------------'
echo 'rails_best_practices .'
rails_best_practices .

echo '----------'
echo 'gemsurance'
gemsurance
echo 'The Gemsurance Report is in gemsurance_report.html in the root directory.'
```
* The Gemsurance Report shows which gems are up to date, which are out of date, and which have known security issues and thus more urgently need to be updated.
* Add the following line to the end of .gitignore:
```
gemsurance_report.html
```
* In the .rubocop.yml file, add the tmp/vulnerabilities/lib/* files, tmp/vulnerabilities/spec/* files, and tmp/vulnerabilities/Rakefile to the list of AllCops exclusions.  (These files are automatically generated when you run the test_code.sh script.)  The .rubucop.yml file should look like:
```
AllCops:
  Exclude:
    - db/schema.rb
    - tmp/vulnerabilities/lib/*
    - tmp/vulnerabilities/spec/*
    - tmp/vulnerabilities/Rakefile
    
Metrics/LineLength:
. . .
```
* Enter the command "sh test_code.sh" to run this script.
* Enter the command "sh git_check.sh".  You'll find that gemsurance_report.html should be added to .gitignore, and you'll find that the app is no longer RuboCop compliant, because RuboCop examines the files in the tmp directory.

* Enter the following commands:
```
sh git_check.sh
rubocop; rails_best_practices
git add .
git commit -m "Added test_code.sh; updated .gitignore and .rubocop.yml" 
```

### git_check.sh
* In the git_check.sh file, replace the lines between "sh build_fast.sh" and the "git status" section with the following:
```
echo '----------------'
echo 'brakeman -Aq -w2'
brakeman -Aq -w2

echo '-------'
echo 'rubocop'
rubocop

echo '----------------------'
echo 'rails_best_practices .'
rails_best_practices .
```
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Updated git_check.sh" 
```

### all.sh
* Create the file all.sh with the following content:
```
#!/bin/bash

sh build_fast.sh 2>&1 | tee log/build_fast.log
sh test_code.sh 2>&1 | tee log/test_code.log

echo 'Results are logged in:'
echo 'log/build_fast.log'
echo 'log/test_code.log'
```
* Run this script by entering "sh all.sh".
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Added all.sh"
```

### Wrapping Up
* Enter the command "sh git_check.sh". There should be no new files or changes left to add, and the app should be compliant with Brakeman, RuboCop, and Rails Best Practices.
* Enter the command "git push origin 01-05-other_metrics".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
```
