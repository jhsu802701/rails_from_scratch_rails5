# Unit 1
## Chapter 6: Basic Testing

### New Branch
Enter the command "git checkout -b 01-06-basic_test"

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
* Enter the command "rails test'.  You'll see that the test for the title and heading fail.
* Add the following code to the end of the Gemfile:
```
# BEGIN: gems needed for "rails test"
group :testing do
  gem 'minitest-reporters', '1.1.11' # Show red and green by default
end
# END: gems needed for "rails test"

```
* Edit the test/test_helper.rb file.  Prior to the line "class ActiveSupport::TestCase", enter the following lines:
```
require 'minitest/reporters'
Minitest::Reporters.use!

```
* Enter the command "bundle install; rails test".

### SimpleCov

### Wrapping Up

D. Reset
