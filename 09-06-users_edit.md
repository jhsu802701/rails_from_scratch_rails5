# Unit 9
## Chapter 6: Users Edit

### New Branch
Enter the command "git checkout -b 09-06-users_edit".

### Integration Test
* Enter the command "rails generate integration_test users_edit".
* Replace the contents of the file test/integration/users_edit_test.rb with the following:
```
# rubocop:disable Metrics/AbcSize
# rubocop:disable Metrics/MethodLength
# rubocop:disable Metrics/ParameterLists

require 'test_helper'

class UsersEditTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  # Edit all parameters except email
  def edit_all_but_email(u, uname, fname, lname, password_n, password_c)
    edit_user_start(u)
    fill_in('Username', with: uname)
    fill_in('First name', with: fname)
    fill_in('Last name', with: lname)
    fill_in('user_password', with: password_n)
    fill_in('user_password_confirmation', with: password_n)
    fill_in('Current password', with: password_c)
    click_button('Update')
    assert page.has_text?('Your account has been updated successfully.')
    click_on 'Edit Settings'
    page.assert_selector(:xpath, xpath_input_str(uname))
    page.assert_selector(:xpath, xpath_input_str(fname))
    page.assert_selector(:xpath, xpath_input_str(lname))
    click_on 'Logout'
    login_user(uname, password_n, false)
    assert page.has_text?('Signed in successfully.')
    click_on 'Logout'
  end

  # Edit all parameters, including email
  def edit_all(u, uname, e, fname, lname, password_n, password_c)
    edit_user_start(u)
    fill_in('Username', with: uname)
    fill_in('Email', with: e)
    fill_in('First name', with: fname)
    fill_in('Last name', with: lname)
    fill_in('user_password', with: password_n)
    fill_in('user_password_confirmation', with: password_n)
    fill_in('Current password', with: password_c)
    click_button('Update')
    assert page.has_text?('You updated your account successfully,')
    assert page.has_text?('but we need to verify your new email address.')
    assert page.has_text?('Please check your email and follow the confirm link')
    assert page.has_text?('to confirm your new email address.')

    # Confirm new email address
    open_email(e)
    current_email.click_link 'Confirm my account'
    assert page.has_text?('Your email address has been successfully confirmed.')
    clear_emails # Clear the message queue

    # Check new settings
    visit_root
    click_on 'Edit Settings'
    page.assert_selector(:xpath, xpath_input_str(uname))
    page.assert_selector(:xpath, xpath_input_str(fname))
    page.assert_selector(:xpath, xpath_input_str(lname))
    click_on 'Logout'

    login_user(uname, password_n, false)
    assert page.has_text?('Signed in successfully.')
    click_on 'Logout'
  end

  test 'user can access the page for editing settings' do
    login_as(@u1, scope: :user)
    visit root_path
    assert page.has_link?('Edit Settings', href: edit_user_registration_path(@u1))
    visit about_path
    assert page.has_link?('Edit Settings', href: edit_user_registration_path(@u1))
    visit contact_path
    assert page.has_link?('Edit Settings', href: edit_user_registration_path(@u1))
  end

  test 'user edit page has the expected content' do
    login_as(@u1, scope: :user)
    visit root_path
    click_on 'Edit Settings'
    assert page.has_css?('title', text: full_title('User Edit'), visible: false)
    assert page.has_css?('h1', text: 'User Edit')
    assert page.has_text?('password management program')
    assert page.has_text?('create much better passwords')
    assert page.has_link?('KeePassX', href: 'http://www.keepassx.org')
  end

  test 'user can edit all parameters besides email' do
    edit_all_but_email(@u1, 'jbond', 'James', 'Bond',
                       'You Only Live Twice', 'Goldfinger')
  end

  test 'user can edit all parameters, including email' do
    edit_all(@u1, 'jbond', '007@example.com',
             'James', 'Bond', 'You Only Live Twice', 'Goldfinger')
  end

  test 'user can delete self' do
    assert_difference 'User.count', -1 do
      edit_user_start(@u7)
      click_on 'Cancel my account'
      assert page.has_text?('Your account has been successfully cancelled.')
    end
  end
end

# rubocop:enable Metrics/AbcSize
# rubocop:enable Metrics/MethodLength
# rubocop:enable Metrics/ParameterLists
```
* Enter the command "rails test".  All 5 of the new integration tests will fail due to undefined methods.
* Enter the command "alias test1='(command from test results minus the TESTOPTS portion)'".
* Enter the command "test1".

### Test Helper
Define the undefined methods of the integration test by adding these lines to the end of the test/test_helper.rb file:
```
# Needed for using Devise tools in testing, such as login_as
include Warden::Test::Helpers

def edit_user_start(user1)
  login_as(user1, scope: :user)
  visit root_path
  assert page.has_link?('Edit Settings', href: edit_user_registration_path(user1))
  click_on 'Edit Settings'
  assert page.has_css?('title', text: full_title('User Edit'), visible: false)
  assert page.has_css?('h1', text: 'User Edit')
  assert page.has_text?('password management program')
  assert page.has_text?('create much better passwords')
  assert page.has_link?('KeePassX', href: 'http://www.keepassx.org')
end

def xpath_input_str(str_input)
  str1 = './/input[@value="'
  str2 = str_input
  str3 = '"]'
  output = "#{str1}#{str2}#{str3}"
  output
end
```

### Header
* Replace the user portion of the file app/views/layouts/_header.html.erb with the following:
```
          <%-##################### -%>
          <%-# BEGIN: USER SECTION -%>
          <%-##################### -%>
          <li class="dropdown">
            <a href="#" class="dropdown-toggle" data-toggle="dropdown">
              User<br>Menu <b class="caret"></b>
            </a>
            <ul class="dropdown-menu">
              <li><%= link_to "Edit Settings", edit_user_registration_path(current_user) %></li>
              <li class="divider"></li>
              <li>
                <%= link_to 'Logout', destroy_user_session_path, :method=>'delete' %>
              </li>
            </ul>
          </li>
          <%-################### -%>
          <%-# END: USER SECTION -%>
          <%-################### -%>
```
* Enter the command "test1".  The first test passes, but the other 4 still fail.
* In theory, you should be able to access the link to edit settings from the menu bar.  If you use the "view source" option in your browser, you'll see that the link is there.  In practice, you cannot access it.

### app/assets/javascripts/application.js
* Replace the content in the app/assets/javascripts/application.js file with the following:
```
// This is a manifest file that'll be compiled into application.js, which will include all the files
// listed below.
//
// Any JavaScript/Coffee file within this directory, lib/assets/javascripts, vendor/assets/javascripts,
// or any plugin's vendor/assets/javascripts directory can be referenced here using a relative path.
//
// It's not advisable to add code directly here, but if you do, it'll appear at the bottom of the
// compiled file. JavaScript code in this file should be added after the last require_* statement.
//
// Read Sprockets README (https://github.com/rails/sprockets#sprockets-directives) for details
// about supported directives.
//
//= require jquery
//= require jquery_ujs
//= require bootstrap
//= require turbolinks
//= require_tree .
```
* Users can now access the link to edit settings from the menu bar.  The next step is to customize the page where users can edit their settings.

### User Edit Page
* Edit the app/views/users/registrations/edit.html.erb and replace its content with the following:
```

```
* At this point, the updating process does not yet work.  (Do NOT be fooled by the message stating that you successfully changed your settings, because you'll see when you click on the "Edit Settings" button that the old settings are still in place.)  The next step is to update the user registration controller.

### User Registration Controller

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
