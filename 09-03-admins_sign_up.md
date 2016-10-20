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
* In your web browser, go to the URL http://localhost:3000/admins/sign_up (replacing the "localhost" and "3000" if necessary).  You'll still see the generic sign up form provided by Devise, but the debug window will show the "admins/registrations" controller instead.

### Admin Registration Controller
* Edit the app/controllers/admins/registrations_controller.rb file.
* Add the line "#" immediately above the line that begins with "class Admins".
* Replace the "# GET /resource/sign_up" and "# POST /resource" sections with the following:
```
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
```
* Enter the command "test1".  The test should now pass.
* In your browser window, go to the URL http://localhost:3000/admins/sign_up (replacing "localhost" and/or "3000" if necessary).  You should be automatically forwarded to the home page, and you should see the message "Admin sign-ups are disabled." highlighted in red.


### Admin Sign Up Page
Delete the Admin Sign Up page, because it is no longer necessary.  Enter the command "git rm app/views/admins/registrations/new.html.erb".

### Rails Best Practices
* Entering the command "rails_best_practices" falsely shows unused methods in app/controllers/admins/registration
s_controller.rb.  You need to suppress this warning.
* Enter the command "rails_best_practices -g".  This generates the file config/rails_best_practices.yml.
* Edit the file config/rails_best_practices.yml.  Replace the line beginning with "RemoveUnusedMethodsInControllersCheck" with the following:
```
RemoveUnusedMethodsInControllersCheck: { except_methods: [],
                                         ignored_files: ['app/controllers/admins/registrations_controller'] }
```
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
