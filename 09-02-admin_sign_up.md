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
  visit new_admin_registration_path

  # Flash message
  assert page.has_text?('Admin sign-ups are disabled.')

  # Home page
  assert page.has_css?('title', text: full_title(''), visible: false)
  assert page.has_css?('h1', text: 'Home')
end

```
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
* 
```
### Stylesheet
Add the following lines to the end of the file app/assets/stylesheets/custom.scss:

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

## 
