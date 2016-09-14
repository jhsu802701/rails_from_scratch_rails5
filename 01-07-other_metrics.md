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
  gem 'gemsurance'
end
# END: gems used in test_code.sh script
```
* Enter the command "bundle install" to install the gems.
* Enter the following commands:
```
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
* 

### bundle-audit

### Brakeman

### sandi_meter

### Rails Best Practices

### G. Gemsurance

### H. test_code.sh

### I. all.sh

### J. git_check.sh

### K.  Wrapping Up
