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

class AdminsLoginTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  def post_admin_login(username)
    # Flash
    assert page.has_text?('Signed in successfully.')

    # Successful login -> home page
    assert page.has_css?('title', text: full_title(''), visible: false)
    assert page.has_css?('h1', text: 'Home')

    # Special message on home page
    assert page.has_text?("You are logged in as an admin (#{username}).")
  end

  test 'User login page provides access to admin login page' do
    visit root_path
    click_on 'Login'
    assert page.has_link?('Admin Login', href: new_admin_session_path)
  end

  test 'Admin login page has expected content' do
    visit root_path
    click_on 'Login'
    click_on 'Admin Login'
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
                            sessions: 'users/sessions' }
  # END: admin
```
* In your web browser, refresh the admin login page. You'll still see the generic Devise sign in page, but the controller is now "admins sessions".

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
        </li>
        <% elsif user_signed_in? %>
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

        <li><%= link_to "About",   about_path %></li>
        <li><%= link_to "Contact",   contact_path %></li>
      </ul>
    </nav>
  </div>
</header>
```

### Home Page
* Edit the file app/views/static_pages/home.html.erb and replace its contents with the following:
```
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
