# Unit 3
## Chapter 1: Basic Testing
In this chapter, you will set up basic testing with Minitest.

### New Branch
Enter the command "git checkout -b 03-01-basic_test"

### Gemfile
*  Add the following lines to the end of the Gemfile:
```
# BEGIN: gems needed for "rails test"
group :testing do
  gem 'minitest-reporters', '1.1.11' # Show red and green by default
end
# END: gems needed for "rails test"
```
* Enter the command "bundle install".
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Installed minitest-reporters and simplecov"
```
### test/test_helper.rb
*  Replace the contents of the file test/test_helper.rb with the following code:
```
ENV['RAILS_ENV'] ||= 'test'
require File.expand_path('../../config/environment', __FILE__)
require 'rails/test_help'

# BEGIN: use minitest-reporters
require 'minitest/reporters'
Minitest::Reporters.use!
reporter_options = { color: true, slow_count: 10 }
Minitest::Reporters.use! [Minitest::Reporters::DefaultReporter.new(reporter_options),
                          Minitest::Reporters::HtmlReporter.new]
# END: use minitest-reporters

class ActiveSupport::TestCase
  # Setup all fixtures in test/fixtures/*.yml for all tests in alphabetical order.
  fixtures :all

  # Add more helper methods to be used by all tests here...
end
```

* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Updated test/test_helper.rb for minitest-reporters"
```

### First Test
* Enter the command "rails generate integration_test test1".  This creates the file test/integration/test1_test.rb.
* Give test/integration/test1_test.rb the following content:
```
require 'test_helper'

class Test1Test < ActionDispatch::IntegrationTest
  test 'home page has expected title and heading' do
    get '/'
    assert_select 'title', 'Home'
    assert_select 'h1', 'Home'
  end

  test 'home page has expected content' do
    get '/'
    assert_match 'Welcome to public/index.html!', response.body
  end
end
```
* Enter the command "rails test".  You'll see that the test for the title and heading fail.
* Replace the contents of the public/index.html file with the following:
```
<html>
    <head>
        <title>Home</title>
    </head>
    <body>
	<h1>Home</h1>	
    Welcome to public/index.html!
    </body>
</html>
```
* Now all of the tests pass, and the display output contains green instead of red.
* Enter the following commands:
```
sh git_check.sh
git add .
git commit -m "Successfully completes first test"
```

### Wrapping Up
* Enter the command "sh git_check.sh". There should be no new files or changes left to add.
* Enter the command "git push origin 03-01-basic_test".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
```
