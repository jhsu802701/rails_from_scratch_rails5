# Unit 9
## Chapter 2: New Admin Sign Up

In this chapter, you will disable the new user registration capability.  Anyone who visits the URL for registering a new admin should be automatically forwarded to the home page and see a flash message noting that new admin registrations are disabled.

### New Branch
Enter the command "git checkout -b 09-02-admin_sign_up".

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

### Routing
Edit the config/routes.rb file.  Replace the line "devise_for :admins" with the following:
```
  # BEGIN: admin
  devise_for :admins,
             controllers: { registrations: 'admins/registrations' }
  # END: admin
```
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
### Stylesheet
* Add the following lines to the end of the file app/assets/stylesheets/custom.scss:

```
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
* In your browser window, go to the URL http://localhost:3000/admins/sign_up (replacing "localhost" and/or "3000" if necessary).  You should be automatically forwarded to the home page, and you should see the message "Admin sign-ups are disabled." highlighted in red.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Disabled admin sign-up"
git push origin 09-02-admin_sign_up
```
### Wrapping Up
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
