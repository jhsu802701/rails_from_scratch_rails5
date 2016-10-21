# Unit 9
## Chapter 6: Users Edit

### New Branch
Enter the command "git checkout -b 09-06-users_edit".

### Integration Test
* Enter the command "rails generate integration_test users_edit".
* Replace the contents of the file test/integration/users_edit_test.rb with the following:
```
ENV['RAILS_ENV'] ||= 'test'
require File.expand_path('../../config/environment', __FILE__)
require 'rails/test_help'

# BEGIN: use minitest-reporters
# AwesomeReporter configuration from
# http://chriskottom.com/blog/2014/06/dress-up-your-minitest-output/
# RakeRerunReporter from
# https://gist.github.com/foton/141b9f73caccf13ccfcc

require 'minitest/reporters'
require 'rake_rerun_reporter'
Minitest::Reporters.use!

module Minitest
  module Reporters
    class AwesomeReporter < DefaultReporter
      GREEN = '1;32'.freeze
      RED = '1;31'.freeze

      def color_up(string, color)
        color? ? "\e\[#{color}m#{string}#{ANSI::Code::ENDCODE}" : string
      end

      def red(string)
        color_up(string, RED)
      end

      def green(string)
        color_up(string, GREEN)
      end
    end
  end
end

reporter_options = { color: true, slow_count: 5, verbose: false, rerun_prefix: 'rm -f log/test.log && bundle exec' }
Minitest::Reporters.use! [Minitest::Reporters::AwesomeReporter.new(reporter_options),
                          Minitest::Reporters::HtmlReporter.new,
                          Minitest::Reporters::RakeRerunReporter.new(reporter_options)]
# END: use minitest-reporters

class ActiveSupport::TestCase
  # Setup all fixtures in test/fixtures/*.yml for all tests in alphabetical order.
  fixtures :all

  # Add more helper methods to be used by all tests here...
end

#######################
# BEGIN: Capybara setup
#######################
require 'capybara/rails'
require 'capybara/email'

class ActionDispatch::IntegrationTest
  # Make the Capybara DSL available in all integration tests
  include Capybara::DSL
  include Capybara::Email::DSL

  # Load up test fixtures at the beginning of each test
  # rubocop:disable Metrics/AbcSize
  # rubocop:disable Metrics/MethodLength
  def setup
    @a1 = admins(:elle_woods)
    @a2 = admins(:vivian_kensington)
    @a3 = admins(:emmett_richmond)
    @a4 = admins(:paulette_bonafonte)
    @a5 = admins(:professor_callahan)
    @a6 = admins(:warner_huntington)

    @u1 = users(:connery)
    @u2 = users(:lazenby)
    @u3 = users(:moore)
    @u4 = users(:dalton)
    @u5 = users(:brosnan)
    @u6 = users(:craig)
    @u7 = users(:blofeld)
  end
  # rubocop:enable Metrics/AbcSize
  # rubocop:enable Metrics/MethodLength

  # Reset sessions and driver between tests
  # Use super wherever this method is redefined in your individual test classes
  def teardown
    Capybara.reset_sessions!
    Capybara.use_default_driver
  end
end
#######################
# END: Capybara setup
#######################

# rubocop:disable Metrics/AbcSize
# rubocop:disable Metrics/MethodLength
# rubocop:disable Metrics/ParameterLists
def sign_up_user(name_u, name_l, name_f, e, p1, p2)
  visit root_path
  assert page.has_link?('Sign up now!', href: new_user_registration_path)
  click_on 'Sign up now!'
  assert page.has_css?('title', text: full_title('New User'), visible: false)
  assert page.has_css?('h1', text: 'New User')
  assert page.has_text?('password management program')
  assert page.has_text?('create much better passwords')
  assert page.has_link?('KeePassX', href: 'http://www.keepassx.org')
  fill_in('Last name', with: name_l)
  fill_in('First name', with: name_f)
  fill_in('Username', with: name_u)
  fill_in('Email', with: e)
  fill_in('Password', with: p1) # not yet changed
  fill_in('Password confirmation', with: p2)
  click_button('Sign up')
end
# rubocop:enable Metrics/AbcSize
# rubocop:enable Metrics/MethodLength
# rubocop:enable Metrics/ParameterLists

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

# Needed for using Devise tools in testing, such as login_as
include Warden::Test::Helpers

# rubocop:disable Metrics/AbcSize
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
# rubocop:enable Metrics/AbcSize

def xpath_input_str(str_input)
  str1 = './/input[@value="'
  str2 = str_input
  str3 = '"]'
  output = "#{str1}#{str2}#{str3}"
  output
end
```
* Enter the command "rails test".  All 5 of the new integration tests will fail due to undefined methods.
* Enter the command "alias test1='(command from test results minus the TESTOPTS portion)'".
* Enter the command "test1".

### Test Helper
Define the undefined methods of the integration test by adding these lines to the end of the test/test_helper.rb file:
```
# Needed for using Devise tools in testing, such as login_as
include Warden::Test::Helpers

# rubocop:disable Metrics/AbcSize
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
# rubocop:enable Metrics/AbcSize

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
          <% ##################### %>
          <% # BEGIN: USER SECTION %>
          <% ##################### %>
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
          <% ################### %>
          <% # END: USER SECTION %>
          <% ################### %>
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
* The "require bootstrap" part is the critical part that allows the dropdown menu to work properly.

### User Edit Page
* Edit the app/views/users/registrations/edit.html.erb and replace its content with the following:
```
<% provide(:title, "User Edit") %>

<h1>User Edit</h1>

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
* Enter the command "test1".  3 tests pass, but 2 tests still fail because the process of updating user settings does not work.  (If you try it out in the browser, do NOT be fooled by the message stating that you successfully changed your settings, because you'll see when you click on the "Edit Settings" button that the old settings are still in place.)  The next step is to update the user registration controller.

### User Registration Controller
* Replace the contents of the file /app/controllers/users/registrations_controller.rb with the following:
```
#
class Users::RegistrationsController < Devise::RegistrationsController
  before_action :configure_sign_up_params, only: [:create]
  before_action :configure_account_update_params, only: [:update]

  # GET /resource/sign_up
  # def new
  #   super
  # end

  # POST /resource
  # def create
  #   super
  # end

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
  def configure_sign_up_params
    devise_parameter_sanitizer.permit(:sign_up,
                                      keys: [:username, :last_name,
                                             :first_name, :email])
  end

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
* Enter the command "test1".  Now all of the tests should pass.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added user update capability"
```

### Wrapping Up
* Enter the command "git push origin 09-06-users_edit".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
