# Unit 9
## Chapter 4: Users Login

### New Branch
Enter the command "git checkout -b 09-04-users_login".

### Integration Test
* Enter the command "rails generate integration_test users_login".
* Replace the contents of test/integration/users_login_test.rb with the following:
```
require 'test_helper'

class UsersLoginTest < ActionDispatch::IntegrationTest
  def post_user_login(username)
    # Flash
    assert page.has_text?('Signed in successfully.')

    # Successful login -> home page
    assert page.has_css?('title', text: full_title(''), visible: false)
    assert page.has_css?('h1', text: 'Home')

    # Special message on home page
    assert page.has_text?("You are logged in as a user (#{username}).")
  end

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
    post_user_login('sconnery')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  test 'Successful user login and logout, with remembering' do
    login_user('sconnery', 'Goldfinger', true)
    post_user_login('sconnery')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  test 'Proper response for new user login and logout' do
    sign_up_user('jhiggins', 'Higgins', 'Jonathan',
                 'jhiggins@example.com', 'Zeus and Apollo',
                 'Zeus and Apollo')

    # Logging in before confirmation
    login_user('jhiggins', 'Zeus and Apollo', false)
    assert page.has_text?('You have to confirm your email address before continuing.')

    # Confirmation
    open_email('jhiggins@example.com')
    current_email.click_link 'Confirm my account'
    assert page.has_text?('Your email address has been successfully confirmed.')

    # Logging in after confirmation
    login_user('jhiggins', 'Zeus and Apollo', false)
    post_user_login('jhiggins')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end
end
```

* Enter the command "sh build_fast.sh".  All 7 of the new integration tests will fail.
* Enter the command "alias test1='(insert command to rerun failed tests, minus the TESTOPTS portion)'".
* Enter the command "test1".  Five of the tests fail because the method login_user is undefined, and two tests fail because the link to the user login page cannot be found.
* In your web browser, go to the URL http://localhost:3000/users/sign_in (and replace the "localhost" and "3000" if necessary).  You'll see a generic Devise sign in page.  The debug box will show the "devise/sessions" controller and the action "new".

### Test Helper (def login_user)
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
* Enter the command "test1".  All 7 tests fail because the link to the user login page cannot be found.

### Header
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
        <% else %>
          <%-####################### -%>
          <%-# BEGIN: GENERAL PUBLIC -%>
          <%-####################### -%>
          <li><%= link_to 'Login', new_user_session_path %></li>
          <%-##################### -%>
          <%-# END: GENERAL PUBLIC -%>
          <%-##################### -%>
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
* Enter the command "test1".  The first integration test passes, but the rest fail.
* In your web browser, go to your app's home page.  Click on the "Login" link in the header to visit the user sign in page, which is still the generic form provided by Devise.

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

### User Login Form
* Replace the content of the app/views/users/sessions/new.html.erb file with the following:
```
<% provide(:title, "User Login") %>

<h1>User Login</h1>

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

    <%= render "users/shared/links" %>
  </div>
</div>
```
* Enter the command "test1".  The first two integration tests will pass, but the rest will still fail.
* In your web browser, go to the URL http://localhost:3000/users/sign_in (replacing the "localhost" and "3000" if necessary).  Now the desired user login form appears, and you can log in as one of the seeded users.
* When you visit your app as a logged-in user, you'll still see the "Sign up now!" button on the home page. Clicking on that button will simply give you a message telling you that you are already signed in. However, you still need to remove this button, because it looks bad to provide options that are not actually viable.  (You'll get to this later.)

### Home Page
* Replace the contents of the file app/views/static_pages/home.html.erb with the following:
```
<% provide(:title, '') %>

<div class="center jumbotron">
  <h1>Home</h1>
  Welcome to Generic App Template!
  <br><br>
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
  <% else %>
    <div class="center jumbotron">
      <%= link_to "Sign up now!", new_user_registration_path, class: "btn btn-lg btn-primary" %>
    </div>
  <% end %>
  <% ####################### %>
  <% # END: VARIABLE SECTION %>
  <% ####################### %>
  <br><br>
  <%= link_to image_tag("rails.png", alt: "Rails logo"),
              'http://rubyonrails.org/' %>
</div>
```
* Enter the command "test1".  Now all of the tests should pass.


### ???

* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added user login"
```

### Wrapping Up
* Enter the command "git push origin 09-04-users_login".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
