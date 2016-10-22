# Unit 9
## Chapter 8: Users Password Reset

### New Branch
Enter the command "git checkout -b 09-08-users_password_reset".

### Integration Test
* Enter the command "rails generate integration_test users_password_reset".
* Replace the contents of test/integration/users_password_reset_test.rb with the following code:
```
# rubocop:disable Metrics/LineLength

require 'test_helper'

class UsersPasswordResetTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  test 'user password reset page is accessible' do
    visit root_path
    click_on 'Login'
    assert page.has_link?('Forgot your password?', href: new_user_password_path)
  end

  test 'user password reset page has the expected content' do
    visit root_path
    click_on 'Login'
    click_on 'Forgot your password?'
    assert page.has_css?('title', text: full_title('User: Reset Forgotten Password'), visible: false)
    assert page.has_css?('h1', text: 'User: Reset Forgotten Password')
  end

  test 'successfully resets super user password' do
    begin_user_password_reset('sean_connery@example.com')
    assert page.has_text?('instructions on how to reset your password')

    # Open and follow instructions
    open_email('sean_connery@example.com')
    current_email.click_link 'Change my password'
    clear_emails # Clear the message queue

    # Provide new password and log in
    assert page.has_css?('title', text: full_title('User: Change Your Password'),
                                  visible: false)
    assert page.has_css?('h1', text: 'User: Change Your Password')
    assert page.has_text?('password management program')
    assert page.has_text?('create much better passwords')
    assert page.has_link?('KeePassX', href: 'http://www.keepassx.org')
    fill_in('user_password', with: 'James Bond')
    fill_in('user_password_confirmation', with: 'James Bond')
    click_on 'Change my password'
    assert page.has_text?('Your password has been changed successfully.')
    assert page.has_text?('You are now signed in.')
    assert page.has_text?('You are logged in as a user (sconnery).')
    click_on 'Logout'

    # Log in under the normal method
    login_user('sconnery', 'James Bond', false)
    click_on 'Logout'
  end
end

# rubocop:enable Metrics/LineLength
```
* Enter the command "sh build_fast.sh".  The first test will pass, but the other two will fail.
* Enter the command "alias test1='(command provided by test results minus the TESTOPTS portion)'".
* Enter the command "test1".
* In your web browser, go to the Home page.  Click on "Login", and then click on "Forgot your password?".  Note that the controller in the debug box is "devise/passwords".

### Routing
* Replace the user section in config/routes.rb with the following code:
```
  # BEGIN: user section
  devise_for :users,
             controllers: { registrations: 'users/registrations',
                            sessions: 'users/sessions',
                            passwords: 'users/passwords' }
  # END: user section
```
* Refresh the browser.  Now the controller listed in the debug box is "users/passwords".


### app/views/users/passwords/new.html.erb
* Replace the contents of app/views/users/passwords/new.html.erb with the following code:
```
<% provide(:title, "User: Reset Forgotten Password") %>

<h1>User: Reset Forgotten Password</h1>

<%= form_for(resource, as: resource_name, url: password_path(resource_name), html: { method: :post }) do |f| %>
  <%= devise_error_messages! %>

  <div class="field">
    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true %>
  </div>

  <div class="actions">
    <%= f.submit "Send me reset password instructions" %>
  </div>
<% end %>

<%= render "users/shared/links" %>
```
* Enter the command "test1".  Now the first two tests pass, but the third test still fails due to the missing method begin_user_password_reset.
* Refresh the web browser.  Now you see the desired form.

### Test Helper
* Add the following code to the end of test/test_helper.rb:
```
def begin_user_password_reset(e)
  visit root_path
  click_on 'Login'
  click_on 'Forgot your password?'
  fill_in('Email', with: e)
  click_on 'Send me reset password instructions'
end
```
* Enter the command "test1".  The third test still fails, but it's now due to missing content.

### app/views/users/passwords/edit.html.erb
* Replace the contents of app/views/users/passwords/edit.html.erb with the following code:
```
<% provide(:title, "User: Change Your Password") %>

<h1>User: Change Your Password</h1>

Using the same password for all of your accounts is risky.
Limiting yourself to passwords that you can easily remember is risky.
You should use a password management program like <a href='http://www.keepassx.org'>KeePassX</a>
to create much better passwords AND store them in encrypted form.

<%= form_for(resource, as: resource_name, url: password_path(resource_name), html: { method: :put }) do |f| %>
  <%= devise_error_messages! %>
  <%= f.hidden_field :reset_password_token %>

  <div class="field">
    <%= f.label :password, "New password" %><br />
    <% if @minimum_password_length %>
      <em>(<%= @minimum_password_length %> characters minimum)</em><br />
    <% end %>
    <%= f.password_field :password, autofocus: true, autocomplete: "off" %>
  </div>

  <div class="field">
    <%= f.label :password_confirmation, "Confirm new password" %><br />
    <%= f.password_field :password_confirmation, autocomplete: "off" %>
  </div>

  <div class="actions">
    <%= f.submit "Change my password" %>
  </div>
<% end %>

<%= render "users/shared/links" %>
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added user password reset capability"
```

### Wrapping Up
* Enter the command "git push origin 09-08-users_password_reset".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
