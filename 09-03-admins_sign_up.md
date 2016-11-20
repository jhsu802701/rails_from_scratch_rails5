# Unit 9
## Chapter 3: New Admin Sign Up

In this chapter, you will disable the new user registration capability.  Anyone who visits the URL for registering a new admin should be automatically forwarded to the home page and see a flash message noting that new admin registrations are disabled.

### New Branch
Enter the command "git checkout -b 09-03-admins_sign_up".

### Integration Test
* Enter the command "rails generate integration_test admins_signup".
* Replace the contents of the file test/integration/admins_signup_test.rb with the following code:
```
require 'test_helper'

class AdminsSignupTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  test 'Cannot sign up as admin' do
    visit new_admin_registration_path

    # Flash message
    assert page.has_text?('Admin sign-ups are disabled.')

    # Home page
    assert page.has_css?('title', text: full_title(''), visible: false)
    assert page.has_css?('h1', text: 'Home')
  end
end
```
* Enter the command "rails test".  You'll see that this new test fails.
* Enter the following command:
```
alias test1='(command provided in test results with the TESTOPTS part omitted)'
```
* Enter the command "test1" to repeat just the admin signup test.
* In your web browser, go to the URL http://localhost:3000/admins/sign_up (replacing the "localhost" and "3000" if necessary).  You'll see the generic sign up form provided by Devise.  In the debug window, you'll see the "devise/registrations" controller with the method "new".

### Routing
* Edit the config/routes.rb file.  Replace the line "devise_for :admins" with the following:
```
  # BEGIN: admin
  devise_for :admins,
             controllers: { registrations: 'admins/registrations' }
  # END: admin
```
* Refresh your web browser.  You'll see that the URL http://localhost:3000/admins/sign_up (replacing the "localhost" and "3000" if necessary) now shows a routing error with the message "uninitialized constant Admins".  This is because the admin registration controller specified in the routing does not exist.

### Devise Controllers and Views
* Create the admin authentication controllers. Enter the command "rails generate devise:controllers admins". This creates admin authentication controllers.
* In your web browser, visit the URL http://localhost:3000/admins/sign_up . Now the generic admin sign up form appears once again, but the controller is now "admins/registrations".
* Create the admin authentication pages. Enter the command "rails generate devise:views admins".
* Enter the command "rm app/views/admins/registrations/new.html.erb".  You won't need this form, because admin sign-ups will be disabled.

### Admin Registration Controller
* Replace the contents of the app/controllers/admins/registrations_controller.rb file with the following:
```
#
class Admins::RegistrationsController < Devise::RegistrationsController
  # before_action :configure_sign_up_params, only: [:create]
  # before_action :configure_account_update_params, only: [:update]

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

  # protected

  # If you have extra params to permit, append them to the sanitizer.
  # def configure_sign_up_params
  #   devise_parameter_sanitizer.permit(:sign_up, keys: [:attribute])
  # end

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
* Enter the command "test1".  The test should now pass.
* In your browser window, go to the URL http://localhost:3000/admins/sign_up (replacing "localhost" and/or "3000" if necessary).  You should be automatically forwarded to the home page, and you should see the message "Admin sign-ups are disabled." highlighted in red.

### Rails Best Practices
* Entering the command "rails_best_practices" falsely shows unused methods in app/controllers/admins/registrations_controller.rb.  You need to suppress this warning.
* Edit the file config/rails_best_practices.yml.  Replace the line beginning with "RemoveUnusedMethodsInControllersCheck" with the following:
```
RemoveUnusedMethodsInControllersCheck: { except_methods: [],
                                         ignored_files: ['app/controllers/admins/registrations_controller'] }
```

### RuboCop Compliance
* In the .rubocop.yml file, add "app/controllers/admins/*" to the list of files excluded from the Style/ClassAndModuleChildren cop.
* In the .rubocop.yml file, add "app/controllers/admins/*" to the list of files excluded from the Style/CommentIndentation cop.
* Add a new line consisting of just "#" to the beginning of the file app/controllers/admins/registrations_controller.rb.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Disabled admin sign-up"
```
### Wrapping Up
* Enter the command "git push origin 09-03-admins_sign_up".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
