# Unit 9
## Chapter 4: Users Login

### New Branch
Enter the command "git checkout -b 09-04-users_login".

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
    assert page.has_link?('Login', href: new_user_session_path)
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

  test 'Successful new user login and logout' do
    sign_up_user('jhiggins', 'Higgins', 'Jonathan',
                 'jhiggins@example.com', 'Zeus and Apollo',
                 'Zeus and Apollo')
    open_email('jhiggins@example.com')
    current_email.click_link 'Confirm my account'
    assert page.has_text?('Your email address has been successfully confirmed.')
    login_user('jhiggins', 'Zeus and Apollo', false)
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
* In your web browser, go to the URL http://localhost:3000/users/sign_in (and replace the "localhost" and "3000" if necessary).  You'll see a generic Devise sign in page.  The debug box will show the "devise/sessions" controller and the action "new".

### Routing
* In the config/routes.rb file, replace the user section with the following:
```
  # BEGIN: user section
  devise_for :users,
             controllers: { registrations: 'users/registrations',
                            sessions: 'users/sessions' }
  # END: user section
```
* In your web browser, refresh/visit the URL http://localhost:3000/users/sign_in (replacing the "localhost" and "3000" if necessary).  A login form appears.  You'll still see the generic Devise sign in page, but the controller is now "users sessions".

### Home Page
* Replace the contents of app/views/layouts/_header.html.erb with the following:
```
<header class="navbar navbar-fixed-top navbar-inverse">
  <div class="container">
    <%= link_to "sample app", root_path, id: "logo" %>
    <nav>
      <ul class="nav navbar-nav navbar-right">
        <li><%= link_to "Home",   root_path %></li>

        <%-######################### -%>
        <%-# BEGIN: VARIABLE SECTION -%>
        <%-######################### -%>
        <% if user_signed_in? %>
          <%-##################### -%>
          <%-# BEGIN: USER SECTION -%>
          <%-##################### -%>
          <li>
          <%= link_to 'Logout', destroy_user_session_path, :method=>'delete' %>
          </li>
          <%-################### -%>
          <%-# END: USER SECTION -%>
          <%-################### -%>
        </li>
        <% else %>
          <li><%= link_to 'Login', new_user_session_path %></li>
        <% end %>
        <%-####################### -%>
        <%-# END: VARIABLE SECTION -%>
        <%-####################### -%>

        <li><%= link_to "About",   about_path %></li>
        <li><%= link_to "Contact",   contact_path %></li>
      </ul>
    </nav>
  </div>
</header>
```


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
* In your web browser, go to the URL http://localhost:3000/users/sign_in (replacing the "localhost" and "3000" if necessary).  Now the desired user login form appears, and you can log in as one of the seeded users.
* Enter the command "test1".  The test for the expected content on the user signin page will be the only one that passes.

### Test Helper
* Add the following lines to the end of the file test/test_helper.rb:
```
def login_user(str_uname, str_pwd, status_remember)
  visit root_path
  click_on 'Login'
  fill_in('Username', with: str_uname)
  fill_in('Password', with: str_pwd)
  if status_remember == true
    check('Remember me')
  else
    uncheck('Remember me')
  end
  click_button('Log in')
end
```
* Enter the command "test1".

### Wrapping Up
