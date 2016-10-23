# Unit 9
## Chapter 10: Users Resend Confirmation

### New Branch
Enter the command "git checkout -b 09-10-users_resend_conf".

### Integration Test
* Enter the command "rails generate integration_test users_resend_conf".
* Replace the content of test/integration/users_resend_conf_test.rb with the following code:
```
require 'test_helper'

class UsersResendConfTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  test 'resend confirmation for sign up' do
    sign_up_user('magnumpi', 'Magnum', 'Thomas', 'magnum_pi@example.com',
                 'Work the lock!', 'Work the lock!')

    # Check for the message about the account activation link
    assert page.has_text?('A message with a confirmation link')
    assert page.has_text?('has been sent to your email address.')
    assert page.has_text?('Please follow the link to activate your account.')

    clear_emails # Lose email confirmation message

    # Request new confirmation
    visit root_path
    click_on 'Login'
    click_on "Didn't receive confirmation instructions?"
    assert page.has_css?('title', text: full_title('User: Resend Confirmation'),
                                  visible: false)
    assert page.has_css?('h1', text: 'User: Resend Confirmation')
    fill_in('Email', with: 'magnum_pi@example.com')
    click_on 'Resend confirmation instructions'

    # Open and follow instructions
    open_email('magnum_pi@example.com')
    current_email.click_link 'Confirm my account'
    clear_emails # Clear the message queue

    login_user('magnumpi', 'Work the lock!', false)
    assert page.has_text?('Signed in successfully.')
    click_on 'Logout'
  end

  test 'resend confirmation for changing all params' do
    edit_user_start(@u1)
    fill_in('Username', with: 'jbond')
    fill_in('Email', with: '007@example.com')
    fill_in('First name', with: 'James')
    fill_in('Last name', with: 'Bond')
    fill_in('user_password', with: 'From Russia With Love')
    fill_in('user_password_confirmation', with: 'From Russia With Love')
    fill_in('Current password', with: 'Goldfinger')
    click_button('Update')
    assert page.has_text?('You updated your account successfully,')
    assert page.has_text?('but we need to verify your new email address.')
    assert page.has_text?('Please check your email and follow the confirm link')
    assert page.has_text?('to confirm your new email address.')
    click_on 'Logout'

    clear_emails # Lose email confirmation message

    # Request new confirmation
    visit root_path
    click_on 'Login'
    click_on "Didn't receive confirmation instructions?"
    assert page.has_css?('title', text: full_title('User: Resend Confirmation'),
                                  visible: false)
    assert page.has_css?('h1', text: 'User: Resend Confirmation')
    fill_in('Email', with: '007@example.com')
    click_on 'Resend confirmation instructions'

    # Confirm new email address
    open_email('007@example.com')
    current_email.click_link 'Confirm my account'
    assert page.has_text?('Your email address has been successfully confirmed.')
    clear_emails # Clear the message queue

    login_user('jbond', 'From Russia With Love', false)
    assert page.has_text?('Signed in successfully.')
    click_on 'Edit Settings'
    page.assert_selector(:xpath, xpath_input_str('jbond'))
    page.assert_selector(:xpath, xpath_input_str('James'))
    page.assert_selector(:xpath, xpath_input_str('Bond'))
    click_on 'Logout'
  end
end
```

### Routing
* Replace the user section in config/routes.rb with the following code:
```
  # BEGIN: user section
  devise_for :users,
             controllers: { registrations: 'users/registrations',
                            sessions: 'users/sessions',
                            passwords: 'users/passwords',
                            confirmations: 'users/confirmations' }
  # END: user section
```

### Resend Confirmation Form
* Replace the contents of app/views/users/confirmations/new.html.erb with the following code:
```
<% provide(:title, "User: Resend Confirmation") %>

<h1>User: Resend Confirmation</h1>

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

<%= render "users/shared/links" %>
```
* Enter the command "test1".
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Allows user to request another confirmation message"
```

### Wrapping Up
* Enter the command "git push origin 09-10-users_resend_conf".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
