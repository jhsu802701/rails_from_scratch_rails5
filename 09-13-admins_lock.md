# Unit 9
## Chapter 13: Admins Lock
In this chapter, you will configure the app to lock admins after an excessive number of unsuccessful logins, just as you already did for users in the previous chapter.

### New Branch
Enter the command "git checkout -b 09-13-admins_lock".

### Integration Test
* Enter the command "rails generate integration_test admins_lock".
* Replace the contents of test/integration/admins_lock_test.rb with the following code:
```
# rubocop:disable Metrics/ClassLength
# rubocop:disable Metrics/LineLength

require 'test_helper'
require 'timecop'

class AdminsLockTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  N_WARNING = 5 # Number of incorrect logins for triggering warning

  def login_incorrect_super
    login_admin('ewoods', 'Yale Law School', false)
  end

  def login_incorrect_regular
    login_admin('pbonafonte', 'evil stepmother', false)
  end

  def login_correct_super
    login_admin('ewoods', 'endorphins', false)
  end

  def login_correct_regular
    login_admin('pbonafonte', "Neptune's Beauty Nook", false)
  end

  def lock_super
    N_WARNING.times do
      login_incorrect_super
    end
    assert page.has_text?('You have one more attempt before your account is locked.')
    login_incorrect_super
    assert page.has_text?('Your account is locked.')
    login_correct_super
    assert page.has_text?('Your account is locked.')
  end

  def lock_regular
    N_WARNING.times do
      login_incorrect_regular
    end
    assert page.has_text?('You have one more attempt before your account is locked.')
    login_incorrect_regular
    assert page.has_text?('Your account is locked.')
    login_correct_regular
    assert page.has_text?('Your account is locked.')
  end

  test 'unlock request page has expected content' do
    visit root_path
    click_on 'Login'
    click_on 'Admin Login'
    click_on "Didn't receive unlock instructions?"
    assert page.has_css?('title', text: full_title('Admin Unlock'), visible: false)
    assert page.has_css?('h1', text: 'Admin Unlock')
  end

  test 'super admin can be unlocked by time' do
    lock_super

    t_lock = Time.now
    t29 = t_lock + 29.minutes
    t31 = t_lock + 31.minutes

    # 29 minutes after the lock begins
    Timecop.travel(t29)
    login_correct_super
    assert page.has_text?('Your account is locked.')

    # 31 minutes after the lock begins
    Timecop.travel(t31)
    login_correct_super
    assert page.has_text?('Signed in successfully.')
    assert page.has_text?('You are logged in as an admin (ewoods).')
    Timecop.return
  end

  test 'regular admin can be unlocked by time' do
    lock_regular

    t_lock = Time.now
    t29 = t_lock + 29.minutes
    t31 = t_lock + 31.minutes

    # 29 minutes after the lock begins
    Timecop.travel(t29)
    login_correct_regular
    assert page.has_text?('Your account is locked.')

    # 31 minutes after the lock begins
    Timecop.travel(t31)
    login_correct_regular
    assert page.has_text?('Signed in successfully.')
    assert page.has_text?('You are logged in as an admin (pbonafonte).')
    Timecop.return
  end

  test 'super admin can request another unlock link' do
    lock_super

    # Lose email
    clear_emails # Clear the message queue

    # Request unlock instructions
    visit root_path
    click_on 'Login'
    click_on 'Admin Login'
    click_on "Didn't receive unlock instructions?"
    fill_in('Email', with: 'elle_woods@example.com')
    click_on 'Resend unlock instructions'

    # Follow unlock instructions
    open_email('elle_woods@example.com')
    current_email.click_link 'Unlock my account'
    assert page.has_text?('Your account has been unlocked successfully.')
    assert page.has_text?('Please sign in to continue.')
    clear_emails # Clear the message queue

    # Login
    login_correct_super
    assert page.has_text?('Signed in successfully.')
    assert page.has_text?('You are logged in as an admin (ewoods).')
    clear_emails # Clear the message queue
  end

  test 'regular admin can request another unlock link' do
    lock_regular

    # Lose email
    clear_emails # Clear the message queue

    # Request unlock instructions
    visit root_path
    click_on 'Login'
    click_on 'Admin Login'
    click_on "Didn't receive unlock instructions?"
    fill_in('Email', with: 'paulette_bonafonte@example.com')
    click_on 'Resend unlock instructions'

    # Follow unlock instructions
    open_email('paulette_bonafonte@example.com')
    current_email.click_link 'Unlock my account'
    assert page.has_text?('Your account has been unlocked successfully.')
    assert page.has_text?('Please sign in to continue.')
    clear_emails # Clear the message queue

    # Login
    login_correct_regular
    assert page.has_text?('Signed in successfully.')
    assert page.has_text?('You are logged in as an admin (pbonafonte).')
    clear_emails # Clear the message queue
  end
end

# rubocop:enable Metrics/ClassLength
# rubocop:enable Metrics/LineLength
```
* Enter the command "sh build_fast.sh".  

### Routing
* Replace the admin section of config/routes.rb with the following:
```
  # BEGIN: admin
  devise_for :admins,
             controllers: { registrations: 'admins/registrations',
                            sessions: 'admins/sessions',
                            passwords: 'admins/passwords',
                            confirmations: 'admins/confirmations',
                            unlocks: 'admins/unlocks' }
  # END: admin
```
* Refresh your web browser.  The debug box should now show that the controller in use is "admins/unlocks".

### app/views/admins/unlocks/new.html.erb
Replace the contents of app/views/admins/unlocks/new.html.erb with the following:
```
<% provide(:title, "Admin Unlock") %>

<h1>Admin Unlock</h1>

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

<%= render "admins/shared/links" %>
```

### Wrapping Up
* Enter the command "git push origin 09-13-admins_lock".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
