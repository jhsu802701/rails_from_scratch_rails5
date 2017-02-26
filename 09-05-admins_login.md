# Unit 9
## Chapter 5: Admins Login

### New Branch
Enter the command "git checkout -b 09-05-admins_login".

### Integration Test
* Enter the command "rails generate integration_test admins_login".
* Replace the contents of the file test/integration/admins_login_test.rb with the following code:
```
require 'test_helper'

class AdminsLoginTest < ActionDispatch::IntegrationTest
  def post_admin_login(username)
    # Flash
    assert page.has_text?('Signed in successfully.')

    # Successful login -> home page
    assert page.has_css?('title', text: full_title(''), visible: false)
    assert page.has_css?('h1', text: 'Home')

    # Special message on home page
    assert page.has_text?("You are logged in as an admin (#{username}).")
  
    # No "Sign up now!" button
    assert_not page.has_link?('Sign up now!', href: new_user_registration_path)

    # No login option available
    assert_not page.has_link?('Login', href: new_user_session_path)
  end

  test 'User login page provides access to admin login page' do
    visit root_path
    assert_not page.has_text?('You are logged in')
    click_on 'Login'
    assert page.has_link?('Admin Login', href: new_admin_session_path)
  end

  test 'Admin login page has expected content' do
    visit new_admin_session_path
    assert page.has_css?('title', text: full_title('Admin Login'), visible: false)
    assert page.has_css?('h1', text: 'Admin Login')
  end

  test 'Unsuccessful super admin login, no remembering' do
    login_admin('ewoods', 'Yale Law School', false)
    assert page.has_text?('Invalid Username or password.')
  end

  test 'Unsuccessful regular admin login, with remembering' do
    login_admin('ewoods', 'Yale Law School', true)
    assert page.has_text?('Invalid Username or password.')
  end

  test 'Successful super admin login and logout, no remembering' do
    login_admin('ewoods', 'endorphins', false)
    post_admin_login('ewoods')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  test 'Successful regular admin login and logout, no remembering' do
    login_admin('pbonafonte', "Neptune's Beauty Nook", false)
    post_admin_login('pbonafonte')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  test 'Successful super admin login and logout, with remembering' do
    login_admin('ewoods', 'endorphins', true)
    post_admin_login('ewoods')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  test 'Successful regular admin login and logout, with remembering' do
    login_admin('pbonafonte', "Neptune's Beauty Nook", true)
    post_admin_login('pbonafonte')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end
end
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
<div class="row">
  <div class="col-md-6 col-md-offset-3">

    <%= form_for(resource, as: resource_name, url: session_path(resource_name)) do |f| %>
      <div class="field">
        <%= f.label :username %><br />
        <%= f.text_field :username, autofocus: true %>
      </div>

      <div class="field">
        <%= f.label :password %><br />
        <%= f.password_field :password, autocomplete: "off" %>
      </div>

      <div class="field">
        <%= f.label :remember_me, class: "checkbox inline" do %>
          <%= f.check_box :remember_me %>
          <br>
          <span>Remember me on this computer (check the above box)</span>
        <% end %>
      </div>

      <div class="actions">
        <%= f.submit "Log in" %>
      </div>
    <% end %>

    <%= render "admins/shared/links" %>
    </div>
</div>
```
* In your web browser, refresh the admin login page. Now the desired admin login form appears, and you can log in as one of the seeded admins.
* Enter the command "test1".  The test for the expected content on the admin login page will pass, but the other 7 tests will fail.

### Admin Shared Links
Edit the file app/views/admins/shared/_links.html.erb.  Remove the "Sign up" link and the entire if statement containing this link.  This feature is disabled for admins.

### User Login Page
* Now it's time to provide access to the admin login page through the user login page.
* Add the following lines to the end of the file app/views/users/sessions/new.html.erb:
```
<b>
<%= link_to "Admin Login", new_admin_session_path %><br />
</b>
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
  <% ######################### %>
  <% # BEGIN: VARIABLE SECTION %>
  <% ######################### %>
  <% if user_signed_in? %>
    <% ##################### %>
    <% # BEGIN: USER SECTION %>
    <% ##################### %>
    You are logged in as a user (<%= current_user.username %>).
    <% ################### %>
    <% # END: USER SECTION %>
    <% ################### %>
  <% elsif admin_signed_in? %>
    <% ###################### %>
    <% # BEGIN: ADMIN SECTION %>
    <% ###################### %>
    You are logged in as an admin (<%= current_admin.username %>).
    <% #################### %>
    <% # END: ADMIN SECTION %>
    <% #################### %>
  <% else %>
    <div class="center jumbotron">
      <%= link_to "Sign up now!", new_user_registration_path, class: "btn btn-lg btn-primary" %>
    </div>
  <% end %>
  <% ####################### %>
  <% # END: VARIABLE SECTION %>
  <% ####################### %>
```
* Enter the command "test1".  Now the last 4 tests fail because the Logout link is not available.

### Header
* In the file app/views/layouts/_header.html.erb, replace the variable section with the following code:
```
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
        <% elsif admin_signed_in? %>
          <%-###################### -%>
          <%-# BEGIN: ADMIN SECTION -%>
          <%-###################### -%>
          <li>
          <%= link_to 'Logout', destroy_admin_session_path, :method=>'delete' %>
          </li>
          <%-#################### -%>
          <%-# END: ADMIN SECTION -%>
          <%-#################### -%>
        <% else %>
          <li><%= link_to 'Login', new_user_session_path %></li>
        <% end %>
        <%-####################### -%>
        <%-# END: VARIABLE SECTION -%>
        <%-####################### -%>
```
* Enter the command "test1".  Now all of the tests pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
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
