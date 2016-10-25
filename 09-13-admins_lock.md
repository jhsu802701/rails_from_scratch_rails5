# Unit 9
## Chapter 13: Admins Lock
In this chapter, you will configure the app to lock admins after an excessive number of unsuccessful logins, just as you already did for users in the previous chapter.

### New Branch
Enter the command "git checkout -b 09-13-admins_lock".

### Integration Test
* Enter the command "rails generate integration_test admins_lock".
* Replace the contents of test/integration/admins_lock_test.rb with the following code:
```
# rubocop:disable Metrics/LineLength

require 'test_helper'
require 'timecop'

class AdminsLockTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  N_WARNING = 5 # Number of incorrect logins for triggering warning
  N_LOCK = 6 # Number of incorrect logins for triggering lock

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

  test 'warns super admin about account locking' do
    N_WARNING.times do
      login_incorrect_super
    end
    assert page.has_text?('You have one more attempt before your account is locked.')
  end

  test 'warns regular admin about account locking' do
    N_WARNING.times do
      login_incorrect_regular
    end
    assert page.has_text?('You have one more attempt before your account is locked.')
  end

  test 'locks super admin' do
    N_LOCK.times do
      login_incorrect_super
    end
    assert page.has_text?('Your account is locked.')
    login_correct
    assert page.has_text?('Your account is locked.')
  end

  test 'locks regular admin' do
    N_LOCK.times do
      login_incorrect_regular
    end
    assert page.has_text?('Your account is locked.')
    login_correct
    assert page.has_text?('Your account is locked.')
  end

  test 'user can be unlocked by email' do
    N_LOCK.times do
      login_incorrect
    end
    open_email('sean_connery@example.com')
    current_email.click_link 'Unlock my account'
    assert page.has_text?('Your account has been unlocked successfully.')
    assert page.has_text?('Please sign in to continue.')
    clear_emails # Clear the message queue
    login_correct
    assert page.has_text?('Signed in successfully.')
    assert page.has_text?('You are logged in as a user (sconnery).')
    clear_emails # Clear the message queue
  end

  test 'user can be unlocked by time' do
    N_LOCK.times do
      login_incorrect
    end

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
    N_LOCK.times do
      login_incorrect
    end

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

# rubocop:enable Metrics/LineLength
```
### Routing

### app/views/users/unlocks/new.html.erb



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
