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
* Enter the command "sh build_fast.sh".  All 4 of your new integration tests will fail.
* From the error outputs, use the alias command to create the "test1" alias that tests all of these new integration tests.
* Enter the command "test1" to run just these tests again.

### Home Page
* Replace the contents of the app/views/static_pages/home.html.erb with the following:
```
<% provide(:title, '') %>
<div class="center jumbotron">
  <h1>Home</h1>
  Welcome to Generic App Template!
  <br><br>
  <% if user_signed_in? %>
    You are logged in as a user (<%= current_user.username %>).
  <% elsif admin_signed_in? %>
    You are logged in as an admin (<%= current_admin.username %>).
  <% else %>
    <div class="center jumbotron">
      <%= link_to "Sign up now!", new_user_registration_path, class: "btn btn-lg btn-primary" %>
    </div>
  <% end %>
  <%= link_to image_tag("rails.png", alt: "Rails logo"),
              'http://rubyonrails.org/' %>
</div>

```

### Wrapping Up
