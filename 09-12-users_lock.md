# Unit 9
## Chapter 12: Users Lock

### New Branch
Enter the command "git checkout -b 09-12-users_lock".

### Gemfile
* Add the following lines to the end of the Gemfile:
```
group :testing do
  gem 'timecop' # Changes current time, needed for testing the lock duration
end
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added timecop gem"
```


### Integration Test
* Enter the command "rails generate integration_test users_lock".
* with the following code:
```
require 'test_helper'

class UsersLockTest < ActionDispatch::IntegrationTest
  include ApplicationHelper

  N_WARNING = 5 # Number of incorrect logins for triggering warning
  N_LOCK = 6 # Number of incorrect logins for triggering lock

  def login_incorrect
    login_user('sconnery', 'Austin Powers', false)
  end

  def login_correct
    login_user('sconnery', 'Goldfinger', false)
  end
  
  test 'warning about account locking' do
    N_WARNING.times do
      login_incorrect
    end
    assert page.has_text?('You have one more attempt before your account is locked.')
  end

  test 'implements account locking' do
    N_LOCK.times do
      login_incorrect
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

    # Change time of lock to 29 minutes ago
    u = User.find_by(username: 'sconnery')
    u.locked_at = 29.minutes.ago
    puts Time.now.to_i - u.locked_at.to_i
    login_correct
    assert page.has_text?('Your account is locked.')

    # Change time of lock to 31 minutes ago
    u.locked_at = 331.minutes.ago
    puts Time.now.to_i - u.locked_at.to_i
    login_correct
    puts page.body
    assert page.has_text?('Signed in successfully.')
    assert page.has_text?('You are logged in as a user (sconnery).')
  end

  test 'unlock request page has expected content' do
    visit root_path
    click_on 'Login'
    click_on "Didn't receive unlock instructions?"
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

```

### Routing

### Devise Configuration

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
