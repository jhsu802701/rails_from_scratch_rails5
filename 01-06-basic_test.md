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

### SimpleCov

### Wrapping Up

D. Reset
