# Unit 3
## Chapter 2: Scripts for Quick Testing

In this chapter, you will create scripts for testing a small portion of your entire test suite.  

### What's the point?
* As your app grows, the full test script will take longer and longer.  Given how complex and finicky Ruby on Rails can be, testing is mandatory for producing quality work.
* However, having to wait a long time for tests to complete bogs down your progress and can even tempt you to cut corners on your testing by skipping tests.  You should only work on one small part of your app at any given time, and you need a way to focus your testing on only that small portion.
* You are still on the hook to run the entire test suite AFTER you have verified that the small portion you were working on is up to snuff.  In other words, you run the quick testing scripts many times, but you wait until the end of the process to run the full test suite.
* I do not agree with the use of automated Guard testing.  It adds more complexity, and you must program the Guardfile to trigger only certain tests under certain circumstances.  It is better to just run shorter tests initially and to wait until you believe you are ready to commit code before running the entire test suite.

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

* The helper, model, and controller tests are very quick tests.  In contrast, integration tests are the slow ones.  When your focus is on integration testing, you should focus on only one test and run that test exclusively until you are finished with it.  Once you have done this, run the git_check.sh script to execute your entire test suite.
* Use the alias command to automatically convert a simple command into a much more complex action.  Because an app will eventually have many integration tests, and because it would be unwieldy to give each one its own script, it's better to create an alias (which is NOT part of the source code) that runs one specific test.  When you have finished work on a feature and its associated integration test, you repeat the process for a different integration test.
* Enter the command "rails generate integration_test test2".
* Replace the content of the resulting test/integration_test/test2_test.rb file with the following content:
```
require 'test_helper'

class Test2Test < ActionDispatch::IntegrationTest
  test 'about page has expected title and heading' do
    get 'about'
    assert_select 'title', 'About'
    assert_select 'h1', 'About'
  end

  test 'about page has expected content' do
    get 'about'
    assert_match 'Welcome to public/about.html!', response.body
  end
end
```
* Entering the command "rails test" runs all of the tests.  Both of the new tests fail.
* Entering the command "rails test test/integration/test2_test.rb" runs just the tests in test/integration/test2_test.rb.
* Entering the command "alias 'test'='rails test test/integration/test2_test.rb'" means that entering the simple command "test" equates to running the command "rails test test/integration/test2_test.rb".
* Enter the command "test".  Only the tests in the file test/integration/test2_test.rb are executed.
* Create the file public/about.html with the following content:
```
<html>
<header>
	<title>About</title>
</header>
<body>
	<h1>About</h1>
	Welcome to public/about.html!
</body>
</html>
```
* Enter the command "test".  Now the tests in the file test/integration/test2_test.rb pass.
* Enter the command "sh git_check.sh".   All of the tests pass.  The code complies with Brakeman, RuboCop, and Rails Best Practices.  There is nothing to add to .gitignore at this time.  You are now ready to git commit.
* Enter the following commands:
```
git add .
git commit -m "Successfully completes temp2 integration test"
```

### Wrapping Up
* Enter the command "sh git_check.sh". There should be no new files or changes left to add.
* Enter the command "git push origin 03-02-quick_testing".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
