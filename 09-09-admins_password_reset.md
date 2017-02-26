# Unit 9
## Chapter 9: Admins Password Reset

### New Branch
Enter the command "git checkout -b 09-09-admins_password_reset".

### Integration Test
* Enter the command "rails generate integration_test admins_password_reset".
* Replace the contents of test/integration/admins_password_reset_test.rb with the following code:
```
# rubocop:disable Metrics/AbcSize
# rubocop:disable Metrics/MethodLength

require 'test_helper'

class AdminsPasswordResetTest < ActionDispatch::IntegrationTest
  def password_reset(u, e, p)
    begin_admin_password_reset(e)

    # Open and follow instructions
    open_email(e)
    current_email.click_link 'Change my password'
    clear_emails # Clear the message queue

    # Provide new password and log in
    assert page.has_css?('title', text: full_title('Admin: Change Your Password'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Admin: Change Your Password')
    assert page.has_text?('password management program')
    assert page.has_text?('create much better passwords')
    assert page.has_link?('KeePassX', href: 'http://www.keepassx.org')
    fill_in('admin_password', with: p)
    fill_in('admin_password_confirmation', with: p)
    click_on 'Change my password'
    assert page.has_text?('Your password has been changed successfully.')
    assert page.has_text?('You are now signed in.')
    assert page.has_text?("You are logged in as an admin (#{u}).")
    click_on 'Logout'

    # Log in under the normal method
    login_admin(u, p, false)
    click_on 'Logout'
  end

  test 'admin password reset page is accessible' do
    visit root_path
    click_on 'Login'
    click_on 'Admin Login'
    assert page.has_link?('Forgot your password?', href: new_admin_password_path)
  end

  test 'admin password reset page has the expected content' do
    visit root_path
    click_on 'Login'
    click_on 'Admin Login'
    click_on 'Forgot your password?'
    assert page.has_css?('title', text: full_title('Admin: Reset Forgotten Password'), visible: false)
    assert page.has_css?('h1', text: 'Admin: Reset Forgotten Password')
  end

  test 'successfully resets super admin password' do
    password_reset('ewoods', 'elle_woods@example.com', 'Legally Blonde')
  end

  test 'successfully resets regular admin password' do
    password_reset('erichmond', 'emmett_richmond@example.com', 'Legally Blonde')
  end
end

# rubocop:enable Metrics/AbcSize
# rubocop:enable Metrics/MethodLength
```
* Enter the command "sh build_fast.sh".  The first test will pass, but the other three will fail.
* Enter the command "alias test1='(command from test results minus the TESTOPTS portion)'".
* Enter the command "test1".  Two tests fail because the definition of the method begin_admin_password_reset is unavailable, and one test fails because the expected content is not present in the admin password reset page.
* In your web browser, go to the Admin Login page and click on "Forgot your password?".  In the debug box, you'll see that the controller is "devise/passwords".

### Test Helper
* Add the following code to the end of the file test/test_helper.rb:
```
def begin_admin_password_reset(e)
  visit root_path
  click_on 'Login'
  click_on 'Admin Login'
  click_on 'Forgot your password?'
  fill_in('Email', with: e)
  click_on 'Send me reset password instructions'
end
```
* Enter the command "test1".  All three tests fail due to missing content.

### Routing
* Replace the admin section of config/routes.rb with the following code:
```
  # BEGIN: admin
  devise_for :admins,
             controllers: { registrations: 'admins/registrations',
                            sessions: 'admins/sessions',
                            passwords: 'admins/passwords' }
  # END: admin
```
* Refresh your web browser.  In the debug box, you'll see that the controller is now "admins/passwords".

### app/views/admins/passwords/new.html.erb
* Replace the contents of app/views/admins/passwords/new.html.erb with the following code:
```
<% provide(:title, "Admin: Reset Forgotten Password") %>

<h1>Admin: Reset Forgotten Password</h1>

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

<%= render "admins/shared/links" %>
```
* Enter the command "test1".  Now the first two tests pass, but the remaining two tests fail due to the undefined method begin_admin_password_reset.

### app/views/admins/passwords/edit.html.erb
* Replace the contents of app/views/admins/passwords/edit.html.erb with the following code:
```
<% provide(:title, "Admin: Change Your Password") %>

<h1>Admin: Change Your Password</h1>

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

<%= render "admins/shared/links" %>
```
* Enter the command "test1".  All 4 tests should now pass.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added admins password reset capability"
```

### Wrapping Up
* Enter the command "git push origin 09-09-admins_password_reset".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
