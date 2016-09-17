# Unit 3
## Chapter 2: Scripts for Quick Testing

In this chapter, you will create scripts for testing a small portion of your entire test suite.  

### What's the point?
* As your app grows, the full test script will take longer and longer.  Given how complex and finicky Ruby on Rails can be, testing is mandatory for producing quality work.
* However, having to wait a long time for tests to complete bogs down your progress and can even tempt you to cut corners on your testing by skipping tests.  You should only work on one small part of your app at any given time, and you need a way to focus your testing on only that small portion.
* You are still on the hook to run the entire test suite AFTER you have verified that the small portion you were working on is up to snuff.  In other words, you run the quick testing scripts many times, but you wait until the end of the process to run the full test suite.

### New Branch
Enter the command "git checkout -b 03-02-quick_testing".

### Helper/Mailer Testing
* Create the file testh.sh and give it the following content:
```
#!/bin/bash

# NOTE: The bundle install and database migration are skipped here.
# It is assumed that you already completed these steps, which are
# covered in the build_fast.sh script.

# This script tests the helpers and mailers only.

echo '------------------------------------'
echo 'rails test test/helpers test/mailers'
rails test test/helpers test/mailers
```
* Create the file testhl.sh ("l" for longer) and give it the following content:
```
#!/bin/bash

# This is a slightly longer version of testh.sh.

echo '----------------'
echo 'brakeman -Aq -w2'
brakeman -Aq -w2

echo '-------'
echo 'rubocop'
rubocop

echo '----------------------'
echo 'rails_best_practices .'
rails_best_practices .

sh testh.sh
```
* Enter the command "sh testh.sh".  This is the script to use before you are certain that all of your helper/mailer tests pass.
* Enter the command "sh testhl.sh".  This is the script to use for checking code quality after all of your helper/mailer tests pass.
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Added quick helper/mailer test scripts"
```

### Model Testing
* Create the file testm.sh and give it the following content:
```
#!/bin/bash

# NOTE: The bundle install and database migration are skipped here.
# It is assumed that you already completed these steps, which are
# covered in the build_fast.sh script.

# This script tests the models only.

echo '----------------------'
echo 'rails test test/models'
rails test test/models
```
* Create the file testml.sh ("l" for longer) and give it the following content:
```
#!/bin/bash

# This is a slightly longer version of testm.sh.

echo '----------------'
echo 'brakeman -Aq -w2'
brakeman -Aq -w2

echo '-------'
echo 'rubocop'
rubocop

echo '----------------------'
echo 'rails_best_practices .'
rails_best_practices .

sh testm.sh
```
* Enter the command "sh testm.sh".  This is the script to use before you are certain that all of your model tests pass.
* Enter the command "sh testml.sh".  This is the script to use for checking code quality after all of your model tests pass.
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Added quick model test scripts"
```

### Controller Testing
* Create the file testc.sh and give it the following content:
```
#!/bin/bash

# NOTE: The bundle install and database migration are skipped here.
# It is assumed that you already completed these steps, which are
# covered in the build_fast.sh script.

# This script tests the controllers only.

echo '---------------------------'
echo 'rails test test/controllers'
rails test test/controllers
```
* Create the file testcl.sh ("l" for longer) and give it the following content:
```
#!/bin/bash

# This is a slightly longer version of testc.sh.

echo '----------------'
echo 'brakeman -Aq -w2'
brakeman -Aq -w2

echo '-------'
echo 'rubocop'
rubocop

echo '----------------------'
echo 'rails_best_practices .'
rails_best_practices .

sh testc.sh
```
* Enter the command "sh testc.sh".  This is the script to use before you are certain that all of your model tests pass.
* Enter the command "sh testcl.sh".  This is the script to use for checking code quality after all of your model tests pass.
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Added quick controller test scripts"
```

### Integration Testing


### Wrapup
