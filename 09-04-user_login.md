# Unit 9
## Chapter 4: User Login

### New Branch
Enter the command "git checkout -b 09-04-user_login".

### Integration Test
* Enter the command "rails generate integration_test users_login".
* Replace the contents of test/integration/users_login_test.rb with the following:
```
# rubocop:disable Metrics/LineLength

require 'test_helper'

class UsersLoginTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  test 'Home page provides access to user login page' do
    visit root_path
    assert page.has_link?('Login', href: new_user_session)
  end

  test 'User login page has expected content' do
    visit root_path
    click_on 'Login'
    assert page.has_css?('title', text: full_title('User Login'), visible: false)
    assert page.has_css?('h1', text: 'User Login')
  end

  test 'Unsuccessful user login, no remembering' do
    login_user('sconnery', 'Austin Powers', false)
    assert page.has_text?('Invalid Username or password.')
  end

  test 'Unsuccessful user login, with remembering' do
    login_user('sconnery', 'Austin Powers', false)
    assert page.has_text?('Invalid Username or password.')
  end

  test 'Successful user login and logout, no remembering' do
    login_user('sconnery', 'Goldfinger', false)
    assert page.has_text?('Signed in successfully.')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  test 'Successful user login and logout, with remembering' do
    login_user('sconnery', 'Goldfinger', true)
    assert page.has_text?('Signed in successfully.')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end
end

# rubocop:enable Metrics/LineLength
```

* Enter the command "sh build_fast.sh".  All of the new integration tests will fail.
* Enter the command "alias test1='(insert command to rerun failed tests, minus the TESTOPTS portion)'".
* Enter the command "test1".
* In your web browser, go to the URL http://localhost:3000/users/sign_in (replacing the "localhost" and "3000" if necessary).  You'll get a routing error message.

### Routing
* In the config/routes.rb file, replace the user section with the following:
```
  # BEGIN: user section
  devise_for :users,
             controllers: { registrations: 'users/registrations',
                            sessions: 'users/sessions' }
  # END: user section
```
* In your web browser, go to the URL http://localhost:3000/users/sign_in (replacing the "localhost" and "3000" if necessary).  A login form appears.  The next step is to replace the user login form.

### User Login Form
* Replace the content of the app/views/users/sessions/new.html.erb file with the following:
```
<% provide(:title, "User Login") %>

<h1>User Login</h1>

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

<%= render "users/shared/links" %>
```
* In your web browser, go to the URL http://localhost:3000/users/sign_in (replacing the "localhost" and "3000" if necessary).  Now the desired user login form appears, but it doesn't work properly.

### User Sessions Controller
* Edit the file app/controllers/users/sessions_controller.rb and 

### Wrapping Up
