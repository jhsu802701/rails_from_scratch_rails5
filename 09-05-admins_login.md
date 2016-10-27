# Unit 9
## Chapter 5: Admins Login

### New Branch
Enter the command "git checkout -b 09-05-admins_login".

### Integration Test
* Enter the command "rails generate integration_test admins_login".
* Replace the contents of the file test/integration/admins_login_test.rb with the following code:
```
# rubocop:disable Metrics/LineLength

require 'test_helper'
require 'timecop'

class UsersLockTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  N_WARNING = 5 # Number of incorrect logins for triggering warning

  def login_incorrect
    login_user('sconnery', 'Austin Powers', false)
  end

  def login_correct
    login_user('sconnery', 'Goldfinger', false)
  end

  test 'user can be unlocked by time' do
    N_WARNING.times do
      login_incorrect
    end
    assert page.has_text?('You have one more attempt before your account is locked.')

    login_incorrect
    assert page.has_text?('Your account is locked.')

    t_lock = Time.now
    t29 = t_lock + 29.minutes
    t31 = t_lock + 31.minutes

    # 29 minutes after the lock begins
    Timecop.travel(t29)
    login_correct
    assert page.has_text?('Your account is locked.')

    # 31 minutes after the lock begins
    Timecop.travel(t31)
    login_correct
    assert page.has_text?('Signed in successfully.')
    assert page.has_text?('You are logged in as a user (sconnery).')
    Timecop.return
  end

  test 'unlock request page has expected content' do
    visit root_path
    click_on 'Login'
    click_on "Didn't receive unlock instructions?"
    assert page.has_css?('title', text: full_title('User Unlock'), visible: false)
    assert page.has_css?('h1', text: 'User Unlock')
  end

  test 'user can request another unlock link' do
    N_WARNING.times do
      login_incorrect
    end
    assert page.has_text?('You have one more attempt before your account is locked.')

    login_incorrect
    assert page.has_text?('Your account is locked.')

    # Lose email
    clear_emails # Clear the message queue

    # Request unlock instructions
    visit root_path
    click_on 'Login'
    click_on "Didn't receive unlock instructions?"
    fill_in('Email', with: 'sean_connery@example.com')
    click_on 'Resend unlock instructions'

    # Follow unlock instructions
    open_email('sean_connery@example.com')
    current_email.click_link 'Unlock my account'
    assert page.has_text?('Your account has been unlocked successfully.')
    assert page.has_text?('Please sign in to continue.')
    clear_emails # Clear the message queue

    # Login
    login_correct
    assert page.has_text?('Signed in successfully.')
    assert page.has_text?('You are logged in as a user (sconnery).')
    clear_emails # Clear the message queue
  end
end

# rubocop:enable Metrics/LineLength
```
* Enter the command "rails test".  All 8 of these integration tests should fail.
* Enter the command "alias test1='(command in test results minus the TESTOPTS portion)'.
* Enter the command "test1" to implement the new tests only.  Again, all 8 of these integration tests should fail.
* Go to the local admin login page at http://localhost:3000/admins/sign_in.  (Replace the "localhost" and "3000" if necessary.)  You should see a generic Devise login page with the controller "devise/sessions" and the action "new".

### Routing
* Edit the config/routes.rb file and replace the admin section with the following:
```
  # BEGIN: admin
  devise_for :admins,
             controllers: { registrations: 'admins/registrations',
                            sessions: 'admins/sessions' }
  # END: admin
```
* In your web browser, refresh the admin login page. You'll still see the generic Devise sign in page, but the controller is now "admins/sessions".

### Admin Login Form
* Replace the contents of the file app/views/admins/sessions/new.html.erb with the following:
```
<% provide(:title, 'Admin Login') %>

<h1>Admin Login</h1>

<%= form_for(resource, as: resource_name, url: session_path(resource_name)) do |f| %>
  <div class="field">
    <%= f.label :username %><br />
    <%= f.text_field :username, autofocus: true %>
  </div>

  <div class="field">
    <%= f.label :password %><br />
    <%= f.password_field :password, autocomplete: "off" %>
  </div>

  <% if devise_mapping.rememberable? -%>
    <div class="field">
      <%= f.check_box :remember_me %>
      <%= f.label :remember_me %>
    </div>
  <% end -%>

  <div class="actions">
    <%= f.submit "Log in" %>
  </div>
<% end %>

<%= render "admins/shared/links" %>
```
* In your web browser, refresh the admin login page. Now the desired admin login form appears, and you can log in as one of the seeded admins.
* Enter the command "test1".  The test for the expected content on the admin login page will pass, but the other 7 tests will fail.

### User Login Page
* Add the following lines to the end of the file app/views/users/sessions/new.html.erb:
```
<br><br>
<%= link_to "Admin Login", new_admin_session_path %><br />
```
* Enter the command "test1".  The first two tests should pass, but the other 6 will still fail.

### Test Helper
* Add the following lines to the end of the test/test_helper.rb file:
```
# rubocop:disable Metrics/MethodLength
def login_admin(str_uname, str_pwd, status_remember)
  visit root_path
  click_on 'Login'
  click_on 'Admin Login'
  fill_in('Username', with: str_uname)
  fill_in('Password', with: str_pwd)
  if status_remember == true
    check('Remember me')
  else
    uncheck('Remember me')
  end
  click_button('Log in')
end
# rubocop:enable Metrics/MethodLength
```
* Enter the command "test1".  The first 4 tests pass, but the 4 remaining tests fail because not all of the expected content appears on the home page when logged in as an admin.

### Home Page
* In the file app/views/static_pages/home.html.erb replace the variable section with the following code:
```
  <%-######################### -%>
  <%-# BEGIN: VARIABLE SECTION -%>
  <%-######################### -%>
  <% if user_signed_in? %>
    You are logged in as a user (<%= current_user.username %>).
  <% elsif admin_signed_in? %>
    You are logged in as an admin (<%= current_admin.username %>).
  <% else %>
    <div class="center jumbotron">
      <%= link_to "Sign up now!", new_user_registration_path, class: "btn btn-lg btn-primary" %>
    </div>
  <% end %>
  <%-####################### -%>
  <%-# END: VARIABLE SECTION -%>
  <%-####################### -%>
```
* Enter the command "test1".  Now the last 4 tests fail because the Logout link is not available.

### Header
* In the file app/views/layouts/_header.html.erb, replace the variable section with the following code:
```
        <%-######################### -%>
        <%-# BEGIN: VARIABLE SECTION -%>
        <%-######################### -%>
        <% if user_signed_in? %>
          <li>
          <%= link_to 'Logout', destroy_user_session_path, :method=>'delete' %>
          </li>
        <% elsif admin_signed_in? %>
          <li>
          <%= link_to 'Logout', destroy_admin_session_path, :method=>'delete' %>
          </li>
        <% else %>
          <li><%= link_to 'Login', new_user_session_path %></li>
        <% end %>
        <%-####################### -%>
        <%-# END: VARIABLE SECTION -%>
        <%-####################### -%>
```
* Enter the command "test1".  Now all of the tests pass.
* Enter the command "sh git_check.sh".  All tests should pass, and RuboCop and Rails Best Practices should show no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added admin login"
```

### Wrapping Up
* Enter the command "git push origin 09-05-admins_login".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
