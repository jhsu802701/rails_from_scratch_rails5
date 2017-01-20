# Unit 9
## Chapter 12: Users Lock

In this chapter, you will configure the app to lock users after an excessive number of unsuccessful logins.  Locked users receive an email message providing a link to unlock their accounts so that they can log in again.  The purpose of locking accounts is to prevent denial-of-service attacks that can bring down your Rails site.

### New Branch
Enter the command "git checkout -b 09-12-users_lock".

### Gemfile
* Add the following lines to the end of the Gemfile:
```
gem 'timecop', group: :testing # Changes current time, needed for testing the lock duration
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added timecop gem"
```

### Integration Test
* Enter the command "rails generate integration_test users_lock".
* Replace the contents of test/integration/users_lock_test.rb with the following code:
```
require 'test_helper'
require 'timecop'

class UsersLockTest < ActionDispatch::IntegrationTest
  N_WARNING = 5 # Number of incorrect logins for triggering warning

  def login_incorrect
    login_user('sconnery', 'Austin Powers', false)
  end

  def login_correct
    login_user('sconnery', 'Goldfinger', false)
  end

  test 'user can be unlocked by time' do
    N_WARNING.times do
      login_incorrect
    end
    assert page.has_text?('You have one more attempt before your account is locked.')

    login_incorrect
    assert page.has_text?('Your account is locked.')

    t_lock = Time.now
    t29 = t_lock + 29.minutes
    t31 = t_lock + 31.minutes

    # 29 minutes after the lock begins
    Timecop.travel(t29)
    login_correct
    assert page.has_text?('Your account is locked.')

    # 31 minutes after the lock begins
    Timecop.travel(t31)
    login_correct
    assert page.has_text?('Signed in successfully.')
    assert page.has_text?('You are logged in as a user (sconnery).')
    Timecop.return
  end

  test 'unlock request page has expected content' do
    visit root_path
    click_on 'Login'
    click_on "Didn't receive unlock instructions?"
    assert page.has_css?('title', text: full_title('User Unlock'), visible: false)
    assert page.has_css?('h1', text: 'User Unlock')
  end

  test 'user can request another unlock link' do
    N_WARNING.times do
      login_incorrect
    end
    assert page.has_text?('You have one more attempt before your account is locked.')

    login_incorrect
    assert page.has_text?('Your account is locked.')

    # Lose email
    clear_emails # Clear the message queue

    # Request unlock instructions
    visit root_path
    click_on 'Login'
    click_on "Didn't receive unlock instructions?"
    fill_in('Email', with: 'sean_connery@example.com')
    click_on 'Resend unlock instructions'

    # Follow unlock instructions
    open_email('sean_connery@example.com')
    current_email.click_link 'Unlock my account'
    assert page.has_text?('Your account has been unlocked successfully.')
    assert page.has_text?('Please sign in to continue.')
    clear_emails # Clear the message queue

    # Login
    login_correct
    assert page.has_text?('Signed in successfully.')
    assert page.has_text?('You are logged in as a user (sconnery).')
    clear_emails # Clear the message queue
  end
end
```
* Enter the command "sh build_fast.sh".  All 3 of the new integration tests fail.
* Enter the command "alias test1='(command from test results minus TESTOPTS portion)'".
* Enter the command "test1".

### Devise Configuration
* Edit the file config/initializers/devise.rb.  Make the following changes:
  * Uncomment the line "config.lock_strategy = :failed_attempts".
  * Uncomment the line "config.unlock_keys = [:email]".
  * Uncomment the line "config.unlock_strategy = :both".
  * Uncomment the line containing "config.maximum_attempts" and change it to "config.maximum_attempts = 6".
  * Uncomment the line containing "config.unlock_in" and change it to "config.unlock_in = 30.minutes".
  * Uncomment the line "config.last_attempt_warning = true".
* Enter the command "test1".  All but one test should pass.  This remaining failing test concerns the content of the page for requesting a link to unlock a user account.

### Routing
* In your web browser, go to the home page.  Click on "Login", and then click on "Didn't receive unlock instructions?".  The debug box shows that the controller in use is "devise/unlocks".
* In config/routes.rb, replace the user section with the following:
```
  # BEGIN: user section
  devise_for :users,
             controllers: { registrations: 'users/registrations',
                            sessions: 'users/sessions',
                            passwords: 'users/passwords',
                            confirmations: 'users/confirmations',
                            unlocks: 'users/unlocks' }
  # END: user section
```
* Refresh your web browser.  The debug box now shows that the controller in use is "users/unlocks".

### app/views/users/unlocks/new.html.erb
* Replace the contents of app/views/users/unlocks/new.html.erb with the following:
```
<% provide(:title, "User Unlock") %>

<h1>User Unlock</h1>

<%= form_for(resource, as: resource_name, url: unlock_path(resource_name), html: { method: :post }) do |f| %>
  <%= devise_error_messages! %>

  <div class="field">
    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true %>
  </div>

  <div class="actions">
    <%= f.submit "Resend unlock instructions" %>
  </div>
<% end %>

<%= render "users/shared/links" %>
```
* Enter the command "test1".  Now all of the tests shoud pass.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added user locking capability"
```

### Wrapping Up
* Enter the command "git push origin 09-12-users_lock".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
