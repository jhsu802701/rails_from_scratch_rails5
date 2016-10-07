# Unit 9
## Chapter 2: New Users

In this chapter, you will provide the general public the ability to sign up for a user account.

### New Branch
Enter the command "git checkout -b 09-02-new_user".

### Integration Test
* Enter the command "rails generate integration_test users_signup".
* Edit the resulting test/integration/users_signup_test.rb file and replace the content with the following:
```
require 'test_helper'

class UsersSignupTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  test 'Home page provides access to user signup page' do
    visit_root
    assert page.has_link?('Sign up now!', href: new_user_registration_path)
  end

  test 'User signup page has expected content' do
    visit_root
    click_on 'Sign up now!'
    assert page.has_css?('title', text: full_title('New User'), visible: false)
    assert page.has_css?('h1', text: 'New User')
    assert page.has_text?('password management program')
    assert page.has_text?('create much better passwords')
    assert page.has_link?('KeePassX', href: 'http://www.keepassx.org')
  end

  test 'Proper response for invalid sign-up' do
    assert_no_difference 'User.count' do
      sign_up_user('jhiggins', 'Higgins', 'Jonathan',
                   'jhiggins@example.com', 'Zeus and Apollo',
                   'Higgy Baby')
    end
    assert page.has_text?("Password confirmation doesn't match Password")
  end

  test 'Proper response for valid sign-up' do
    assert_difference 'User.count', 1 do
      sign_up_user('jhiggins', 'Higgins', 'Jonathan',
                   'jhiggins@example.com', 'Zeus and Apollo',
                   'Zeus and Apollo')
    end

    # Check for the message about the account activation link
    assert page.has_text?('A message with a confirmation link')
    assert page.has_text?('has been sent to your email address.')
    assert page.has_text?('Please follow the link to activate your account.')
  end
end
```
* Enter the command "sh build_fast.sh".  All of your new integration tests will fail.
### 

### Wrapping Up
