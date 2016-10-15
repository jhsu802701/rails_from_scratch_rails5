# Unit 9
## Chapter 3: User Sign Up

In this chapter, you will provide the general public the ability to sign up for a user account.

### New Branch
Enter the command "git checkout -b 09-03-user_sign_up".

### Integration Test
* Enter the command "rails generate integration_test users_signup".
* Edit the resulting test/integration/users_signup_test.rb file and replace the content with the following:
```
require 'test_helper'

class UsersSignupTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  test 'Home page provides access to user signup page' do
    visit root_path
    assert page.has_link?('Sign up now!', href: new_user_registration_path)
  end

  test 'User signup page has expected content' do
    visit root_path
    click_on 'Sign up now!'
    assert page.has_css?('title', text: full_title('New User'), visible: false)
    assert page.has_css?('h1', text: 'New User')
    assert page.has_text?('password management program')
    assert page.has_text?('create much better passwords')
    assert page.has_link?('KeePassX', href: 'http://www.keepassx.org')
  end

  test 'Proper response for invalid sign-up' do
    assert_no_difference 'User.count' do
      sign_up_user('jhiggins', 'Higgins', 'Jonathan',
                   'jhiggins@example.com', 'Zeus and Apollo',
                   'Higgy Baby')
    end
    assert page.has_text?("Password confirmation doesn't match Password")
  end

  test 'Proper response for valid sign-up' do
    assert_difference 'User.count', 1 do
      sign_up_user('jhiggins', 'Higgins', 'Jonathan',
                   'jhiggins@example.com', 'Zeus and Apollo',
                   'Zeus and Apollo')
    end

    # Check for the message about the account activation link
    assert page.has_text?('A message with a confirmation link')
    assert page.has_text?('has been sent to your email address.')
    assert page.has_text?('Please follow the link to activate your account.')
  end
end
```
* Enter the command "sh build_fast.sh".  All 4 of your new integration tests will fail.
* From the error outputs, use the alias command to create the "test1" alias that tests all of these new integration tests.
* Enter the command "test1" to run just these tests again.

### Home Page
* Replace the contents of the app/views/static_pages/home.html.erb with the following:
```
<% provide(:title, '') %>
<div class="center jumbotron">
  <h1>Home</h1>
  Welcome to Generic App Template!
  <br><br>
  <% if user_signed_in? %>
    You are logged in as a user (<%= current_user.username %>).
  <% elsif admin_signed_in? %>
    You are logged in as an admin (<%= current_admin.username %>).
  <% else %>
    <div class="center jumbotron">
      <%= link_to "Sign up now!", new_user_registration_path, class: "btn btn-lg btn-primary" %>
    </div>
  <% end %>
  <%= link_to image_tag("rails.png", alt: "Rails logo"),
              'http://rubyonrails.org/' %>
</div>
```
* Enter the command "test1".  The first integration test will pass, but the other three will fail.

## User Signup Page
* Edit the user signup page.  Replace the contents of app/views/users/registrations/new.html.erb with the following:
```
<% provide(:title, "New User") %>

<h1>New User</h1>
Using the same password for all of your accounts is risky.
Limiting yourself to passwords that you can easily remember is risky.
You should use a password management program like <a href='http://www.keepassx.org'>KeePassX</a>
to create much better passwords AND store them in encrypted form.

<%= form_for(resource, html: { multipart: true }, as: resource_name, url: registration_path(resource_name)) do |f| %>
  <%= devise_error_messages! %>

  <div class="field">
    <%= f.label :username %> (for logging in) <br />
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

  <div class="field">
    <%= f.label :password %><br />
    <%= f.password_field :password, autocomplete: "off" %>
  </div>

  <div class="field">
    <%= f.label :password_confirmation %><br />
    <%= f.password_field :password_confirmation, autocomplete: "off" %>
  </div>

  <div class="actions">
    <%= f.submit "Sign up" %>
  </div>
<% end %>

<%= render "users/shared/links" %>
```
* Edit the config/routes.rb file.  Replace the line "devise_for :users" with a user section so that the file looks like:
```
...
Rails.application.routes.draw do
  devise_for :admins
  
  # BEGIN: user section
  devise_for :users,
             controllers: { registrations: 'users/registrations' }
  # END: user section

  root 'static_pages#home'
...
```
* Enter the command "test1".  The first two tests will pass, but the second two tests will still fail.

### Stylesheet
Add the following code to the end of the file app/assets/stylesheets/custom.scss:
```
/* forms */

input, textarea, select, .uneditable-input {
  border: 1px solid #bbb;
  width: 100%;
  margin-bottom: 15px;
  @include box_sizing;
}

input {
  height: auto !important;
}

#error_explanation {
  color: red;
  ul {
    color: red;
    margin: 0 0 30px 0;
  }
}

.field_with_errors {
  @extend .has-error;
  .form-control {
    color: $state-danger-text;
  }
}

.checkbox {
  margin-top: -10px;
  margin-bottom: 10px;
  span {
    margin-left: 20px;
    font-weight: normal;
  }
}

#session_remember_me {
  width: auto;
  margin-left: 0;
}

/*flash*/
.alert-error {
    background-color: #f2dede;
    border-color: #eed3d7;
    color: #b94a48;
    text-align: left;
 }

.alert-alert {
    background-color: #f2dede;
    border-color: #eed3d7;
    color: #b94a48;
    text-align: left;
 }

.alert-success {
    background-color: #dff0d8;
    border-color: #d6e9c6;
    color: #468847;
    text-align: left;
 }

.alert-notice {
    background-color: #dff0d8;
    border-color: #d6e9c6;
    color: #468847;
    text-align: left;
 }
```

### User Registration Controller
* Edit the file app/controllers/users/registrations_controller.rb and replace the content with the following:
```
class Users::RegistrationsController < Devise::RegistrationsController
  before_action :configure_sign_up_params, only: [:create]
  # before_action :configure_account_update_params, only: [:update]

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
    devise_parameter_sanitizer.permit(:sign_up, keys: [:username, :last_name, :first_name, :email])
  end

  # If you have extra params to permit, append them to the sanitizer.
  # def configure_account_update_params
  #   devise_parameter_sanitizer.permit(:account_update, keys: [:attribute])
  # end

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
* At this point, the local server will allow you to sign up, but the second two integration tests will still fail.

### Wrapping Up
