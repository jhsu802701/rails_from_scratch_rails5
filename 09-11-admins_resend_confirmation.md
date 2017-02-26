# Unit 9
# Chapter 11: Admins Resend Confirmation

### New Branch
Enter the command "git checkout -b 09-11-admins_resend_conf".

### Integration Test
* Enter the command "rails generate integration_test admins_resend_conf".
* Replace the contents of test/integration/admins_resend_conf_test.rb with the following code:
```
# rubocop:disable Metrics/AbcSize
# rubocop:disable Metrics/MethodLength
# rubocop:disable Metrics/ParameterLists

require 'test_helper'

class AdminsResendConfTest < ActionDispatch::IntegrationTest
  def edit(a, uname, e, fname, lname, password_n, password_c)
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

    click_on 'Logout'
    clear_emails # Lose the email message

    # Request new confirmation
    visit root_path
    click_on 'Login'
    click_on 'Admin Login'
    click_on "Didn't receive confirmation instructions?"
    assert page.has_css?('title', text: full_title('Admin: Resend Confirmation'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Admin: Resend Confirmation')
    fill_in('Email', with: e)
    click_on 'Resend confirmation instructions'

    # Open and follow instructions
    open_email(e)
    current_email.click_link 'Confirm my account'
    clear_emails # Clear the message queue

    login_admin(uname, password_n, false)
    assert page.has_text?('Signed in successfully.')
    click_on 'Logout'
  end

  test 'resend confirmation from super admin changing all params' do
    edit(@a1, 'rwitherspoon', 'reese_witherspoon@example.com',
         'Reese', 'Witherspoon', 'Just Like Heaven', 'endorphins')
  end

  test 'resend confirmation from regular admin changing all params' do
    edit(@a4, 'jcoolidge', 'jennifer_coolidge@example.com',
         'Jennifer', 'Coolidge', 'A Cinderella Story',
         "Neptune's Beauty Nook")
  end
end

# rubocop:enable Metrics/AbcSize
# rubocop:enable Metrics/MethodLength
# rubocop:enable Metrics/ParameterLists
```
* Enter the command "sh build_fast.sh".  Both tests will fail.
* Enter the command "alias test1='(command in test results minus the TESTOPTS portion)'".
* Enter the command "test1".  Both tests fail because the page where admins request a new confirmation message does not have the expected content.
* In your web browser, go to the home page and click on "Login", "Admin Login", and "Didn't receive confirmation instructions?".  The debug box will show that the controller "devise/confirmations" is in use.

### Routing
* In config/routes.rb, replace the admin section with the following:
```
  # BEGIN: admin
  devise_for :admins,
             controllers: { registrations: 'admins/registrations',
                            sessions: 'admins/sessions',
                            passwords: 'admins/passwords',
                            confirmations: 'admins/confirmations' }
  # END: admin
```
* Refresh your web browser.  The debug box will show that the controller "admins/confirmations" is in use.

### app/views/admins/confirmations/new.html.erb
* Replace the contents of app/views/admins/confirmations/new.html.erb with the following:
```
<% provide(:title, "Admin: Resend Confirmation") %>

<h1>Admin: Resend Confirmation</h1>

<%= form_for(resource, as: resource_name, url: confirmation_path(resource_name), html: { method: :post }) do |f| %>
  <%= devise_error_messages! %>

  <div class="field">
    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true, value: (resource.pending_reconfirmation? ? resource.unconfirmed_email : resource.email) %>
  </div>

  <div class="actions">
    <%= f.submit "Resend confirmation instructions" %>
  </div>
<% end %>

<%= render "admins/shared/links" %>
```
* Enter the command "test1".  Both tests should now pass.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Allows admin to request another confirmation message"
```

### Wrapping Up
* Enter the command "git push origin 09-11-admins_resend_conf".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
