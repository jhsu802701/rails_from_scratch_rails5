# Unit 9
## Chapter 7: Admins Edit

### New Branch
Enter the command "git checkout -b 09-07-admins_edit".

### Integration Test
* Enter the command "rails generate integration_test admins_edit".
* Replace the contents of test/integration/admins_edit_test.rb with the following:
```
# rubocop:disable Metrics/AbcSize
# rubocop:disable Metrics/ClassLength
# rubocop:disable Metrics/LineLength
# rubocop:disable Metrics/MethodLength
# rubocop:disable Metrics/ParameterLists

require 'test_helper'

class AdminsEditTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  # Edit all parameters except email
  def edit_all_but_email(a, uname, fname, lname, password_n, password_c)
    edit_admin_start(a)
    fill_in('Username', with: uname)
    fill_in('First name', with: fname)
    fill_in('Last name', with: lname)
    fill_in('admin_password', with: password_n)
    fill_in('admin_password_confirmation', with: password_n)
    fill_in('Current password', with: password_c)
    click_button('Update')
    assert page.has_text?('Your account has been updated successfully.')
    click_on 'Edit Settings'
    page.assert_selector(:xpath, xpath_input_str(uname))
    page.assert_selector(:xpath, xpath_input_str(fname))
    page.assert_selector(:xpath, xpath_input_str(lname))
    click_on 'Logout'
    login_admin(uname, password_n, false)
    assert page.has_text?('Signed in successfully.')
    click_on 'Logout'
  end

  # Edit all parameters, including email
  def edit_all(a, uname, e, fname, lname, password_n, password_c)
    edit_admin_start(a)
    fill_in('Username', with: uname)
    fill_in('Email', with: e)
    fill_in('First name', with: fname)
    fill_in('Last name', with: lname)
    fill_in('admin_password', with: password_n)
    fill_in('admin_password_confirmation', with: password_n)
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
    visit root_path
    click_on 'Edit Settings'
    page.assert_selector(:xpath, xpath_input_str(uname))
    page.assert_selector(:xpath, xpath_input_str(fname))
    page.assert_selector(:xpath, xpath_input_str(lname))
    click_on 'Logout'

    login_admin(uname, password_n, false)
    assert page.has_text?('Signed in successfully.')
    click_on 'Logout'
  end

  test 'super admin can access the page for editing settings' do
    login_as(@a1, scope: :admin)
    visit root_path
    assert page.has_link?('Edit Settings', href: edit_admin_registration_path(@a1))
    visit about_path
    assert page.has_link?('Edit Settings', href: edit_admin_registration_path(@a1))
    visit contact_path
    assert page.has_link?('Edit Settings', href: edit_admin_registration_path(@a1))
  end

  test 'regular admin can access the page for editing settings' do
    login_as(@a4, scope: :admin)
    visit root_path
    assert page.has_link?('Edit Settings', href: edit_admin_registration_path(@a4))
    visit about_path
    assert page.has_link?('Edit Settings', href: edit_admin_registration_path(@a4))
    visit contact_path
    assert page.has_link?('Edit Settings', href: edit_admin_registration_path(@a4))
  end

  test 'admin edit page has the expected content for super admin' do
    login_as(@a1, scope: :admin)
    visit root_path
    click_on 'Edit Settings'
    assert page.has_css?('title', text: full_title('Admin Edit'), visible: false)
    assert page.has_css?('h1', text: 'Admin Edit')
    assert page.has_text?('password management program')
    assert page.has_text?('create much better passwords')
    assert page.has_link?('KeePassX', href: 'http://www.keepassx.org')
  end

  test 'admin edit page has the expected content for regular admin' do
    login_as(@a4, scope: :admin)
    visit root_path
    click_on 'Edit Settings'
    assert page.has_css?('title', text: full_title('Admin Edit'), visible: false)
    assert page.has_css?('h1', text: 'Admin Edit')
    assert page.has_text?('password management program')
    assert page.has_text?('create much better passwords')
    assert page.has_link?('KeePassX', href: 'http://www.keepassx.org')
  end

  test 'super admin can edit all parameters besides email' do
    edit_all_but_email(@a1, 'rwitherspoon', 'Reese', 'Witherspoon',
                       'Just Like Heaven', 'endorphins')
  end

  test 'regular admin can edit all parameters besides email' do
    edit_all_but_email(@a4, 'jcoolidge', 'Jennifer', 'Coolidge',
                       'A Cinderella Story', "Neptune's Beauty Nook")
  end

  test 'super admin can edit all parameters, including email' do
    edit_all(@a1, 'rwitherspoon', 'reese_witherspoon@example.com',
             'Reese', 'Witherspoon', 'Just Like Heaven', 'endorphins')
  end

  test 'regular admin can edit all parameters, including email' do
    edit_all(@a4, 'jcoolidge', 'jennifer_coolidge@example.com',
             'Jennifer', 'Coolidge', 'A Cinderella Story',
             "Neptune's Beauty Nook")
  end

  test 'super admin can delete self' do
    assert_difference 'Admin.count', -1 do
      edit_admin_start(@a5)
      click_on 'Cancel my account'
      assert page.has_text?('Your account has been successfully cancelled.')
    end
  end

  test 'regular admin can delete self' do
    assert_difference 'Admin.count', -1 do
      edit_admin_start(@a6)
      click_on 'Cancel my account'
      assert page.has_text?('Your account has been successfully cancelled.')
    end
  end
end

# rubocop:enable Metrics/AbcSize
# rubocop:enable Metrics/ClassLength
# rubocop:enable Metrics/LineLength
# rubocop:enable Metrics/MethodLength
# rubocop:enable Metrics/ParameterLists
```
* Enter the command "sh build_fast.sh".  All 10 of the new tests will fail.
* Enter the command "alias test1='(command from test results minus the TESTOPTS portion)'".
* Enter the command "test1".

### Test Helper
* Add the following lines to the end of the file test/test_helper.rb:
```
def edit_admin_start(admin1)
  login_as(admin1, scope: :admin)
  visit root_path
  click_on 'Edit Settings'
end
```
* Enter the command "test1".  All 10 tests will still fail, but the missing definition of edit_admin_start is no longer the culprit.

### Header
* Replace the admin section of app/views/layouts/_header.html.erb with the following code:
```
          <% ###################### %>
          <% # BEGIN: ADMIN SECTION %>
          <% ###################### %>        
          <li class="dropdown">
            <a href="#" class="dropdown-toggle" data-toggle="dropdown">
              Admin<br>Menu <b class="caret"></b>
            </a>
            <ul class="dropdown-menu">
              <li><%= link_to "Edit Settings", edit_admin_registration_path(current_admin) %></li>
              <li class="divider"></li>
              <li>
                <%= link_to 'Logout', destroy_admin_session_path, :method=>'delete' %>
              </li>
            </ul>
          </li>
          <% #################### %>
          <% # END: ADMIN SECTION %>
          <% #################### %>
```
* Enter the command "test1".  The first 4 tests pass, but the second 6 tests still fail.  

### Admin Edit Form
* Replace the contents of app/views/admins/registrations/edit.html.erb with the following code:
```
<% provide(:title, "Admin Edit") %>

<h1>Admin Edit</h1>

Using the same password for all of your accounts is risky.
Limiting yourself to passwords that you can easily remember is risky.
You should use a password management program like <a href='http://www.keepassx.org'>KeePassX</a>
to create much better passwords AND store them in encrypted form.

<%= form_for(resource, as: resource_name, url: registration_path(resource_name), html: { method: :put }) do |f| %>
  <%= devise_error_messages! %>

  <div class="field">
    <%= f.label :username %><br />
    <%= f.text_field :username, autofocus: true %>
  </div>

  <div class="field">
    <%= f.label :last_name %><br />
    <%= f.text_field :last_name %>
  </div>

  <div class="field">
    <%= f.label :first_name %><br />
    <%= f.text_field :first_name %>
  </div>

  <div class="field">
    <%= f.label :email %><br />
    <%= f.email_field :email %>
  </div>

  <% if devise_mapping.confirmable? && resource.pending_reconfirmation? %>
    <div>Currently waiting confirmation for: <%= resource.unconfirmed_email %></div>
  <% end %>

  NOTE: Leave the "new password" and "new password confirmation" fields blank if you do not
  wish to change your password.
  <div class="field">
    <b>New Password</b>
    <% if @minimum_password_length %>
      <br />
      <em><%= @minimum_password_length %> characters minimum</em>
    <% end %>
    <%= f.password_field :password, autocomplete: "off" %>
  </div>

  <div class="field">
    <b>New Password Confirmation</b>
    <%= f.password_field :password_confirmation, autocomplete: "off" %>
  </div>

  <div class="field">
    <%= f.label :current_password %> <i>(we need your current password to confirm your changes)</i><br />
    <%= f.password_field :current_password, autocomplete: "off" %>
  </div>

  <div class="actions">
    <%= f.submit "Update" %>
  </div>
<% end %>

<h3>Cancel my account</h3>

<p>Unhappy? <%= button_to "Cancel my account", registration_path(resource_name), data: { confirm: "Are you sure?" }, method: :delete %></p>

<%= link_to "Back", :back %>
```

### Registration Controller
* Replace the content of app/controllers/admins/registrations_controller.rb with the following code:
```
#
class Admins::RegistrationsController < Devise::RegistrationsController
  # before_action :configure_sign_up_params, only: [:create]
  before_action :configure_account_update_params, only: [:update]

  # GET /resource/sign_up
  def new
    flash[:alert] = 'Admin sign-ups are disabled.'
    redirect_to root_path
  end

  # POST /resource
  def create
    flash[:alert] = 'Admin sign-ups are disabled.'
    redirect_to root_path
  end

  # GET /resource/edit
  # def edit
  #   super
  # end

  # PUT /resource
  # def update
  #   super
  # end

  # DELETE /resource
  # def destroy
  #   super
  # end

  # GET /resource/cancel
  # Forces the session data which is usually expired after sign
  # in to be expired now. This is useful if the user wants to
  # cancel oauth signing in/up in the middle of the process,
  # removing all OAuth session data.
  # def cancel
  #   super
  # end

  protected

  # If you have extra params to permit, append them to the sanitizer.
  # def configure_sign_up_params
  #   devise_parameter_sanitizer.permit(:sign_up, keys: [:attribute])
  # end

  # If you have extra params to permit, append them to the sanitizer.
  def configure_account_update_params
    devise_parameter_sanitizer.permit(:account_update,
                                      keys: [:username, :last_name,
                                             :first_name, :email])
  end

  # The path used after sign up.
  # def after_sign_up_path_for(resource)
  #   super(resource)
  # end

  # The path used after sign up for inactive accounts.
  # def after_inactive_sign_up_path_for(resource)
  #   super(resource)
  # end
end
```

### Wrapping Up
* Enter the command "git push origin 09-07-admins_edit".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
