# Unit 11
## Chapter 1: Admin Profile

In this chapter, you will create admin profile pages. In the interest of privacy, admin profiles will be visible ONLY to admins.

### New Branch
Enter the command "git checkout -b 11-01-admin_show".

### Controller Tests
* Enter the command "rails generate controller Admins new".
* Enter the command "rm app/helpers/admins_helper.rb".
* Enter the command "rm app/views/admins/new.html.erb".
* Replace the contents of test/controllers/admins_controller_test.rb with the following:
```
require 'test_helper'

# rubocop:disable Metrics/ClassLength
class AdminsControllerTest < ActionController::TestCase
  #############
  # BEGIN: show
  #############
  test 'should redirect profile page when not logged in' do
    get :show, params: { id: @a1 }
    assert_redirected_to root_path
    get :show, params: { id: @a2 }
    assert_redirected_to root_path
    get :show, params: { id: @a3 }
    assert_redirected_to root_path
    get :show, params: { id: @a4 }
    assert_redirected_to root_path
    get :show, params: { id: @a5 }
    assert_redirected_to root_path
    get :show, params: { id: @a6 }
    assert_redirected_to root_path
  end

  test 'should redirect users from profile page' do
    sign_in @u1, scope: :user
    get :show, params: { id: @a1 }
    assert_redirected_to root_path
    get :show, params: { id: @a2 }
    assert_redirected_to root_path
    get :show, params: { id: @a3 }
    assert_redirected_to root_path
    get :show, params: { id: @a4 }
    assert_redirected_to root_path
    get :show, params: { id: @a5 }
    assert_redirected_to root_path
    get :show, params: { id: @a6 }
    assert_redirected_to root_path
  end

  test 'should not redirect profile page when logged in as a super admin' do
    sign_in @a1, scope: :admin
    get :show, params: { id: @a1 }
    assert :success
    get :show, params: { id: @a2 }
    assert :success
    get :show, params: { id: @a3 }
    assert :success
    get :show, params: { id: @a4 }
    assert :success
    get :show, params: { id: @a5 }
    assert :success
    get :show, params: { id: @a6 }
    assert :success
  end

  test 'should not redirect profile page when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    get :show, params: { id: @a1 }
    assert :success
    get :show, params: { id: @a2 }
    assert :success
    get :show, params: { id: @a3 }
    assert :success
    get :show, params: { id: @a4 }
    assert :success
    get :show, params: { id: @a5 }
    assert :success
    get :show, params: { id: @a6 }
    assert :success
  end

  # PART 2: INDEX
  test 'should redirect index page when not logged in' do
    get :index
    assert_redirected_to root_path
  end

  test 'should redirect index page when logged in as a user' do
    sign_in @u1, scope: :user
    get :index
    assert_redirected_to root_path
  end

  test 'should not redirect index page when logged in as a super admin' do
    sign_in @a1, scope: :admin
    get :index
    assert :success
  end

  test 'should not redirect index page when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    get :index
    assert :success
  end

  # PART 3: DELETE
  test 'should not allow visitor to delete admin' do
    get :destroy, params: { id: @a5 }
    assert_redirected_to root_path
    get :destroy, params: { id: @a6 }
    assert_redirected_to root_path
  end

  test 'should not allow user to delete admin' do
    sign_in @u1, scope: :user
    get :destroy, params: { id: @a5 }
    assert_redirected_to root_path
    get :destroy, params: { id: @a6 }
    assert_redirected_to root_path
  end

  # NOTE: Admin can delete self through edit registration form.
  test 'should not allow regular admin to delete self' do
    sign_in @a6, scope: :admin
    get :destroy, params: { id: @a6 }
    assert_redirected_to root_path
  end

  # NOTE: Admin can delete self through edit registration form.
  test 'should not allow super admin to delete self' do
    sign_in @a5, scope: :admin
    get :destroy, params: { id: @a5 }
    assert_redirected_to root_path
  end

  test 'should not allow regular admin to delete another regular admin' do
    sign_in @a4, scope: :admin
    get :destroy, params: { id: @a6 }
    assert_redirected_to root_path
  end

  test 'should not allow regular admin to delete super admin' do
    sign_in @a6, scope: :admin
    get :destroy, params: { id: @a5 }
    assert_redirected_to root_path
  end

  test 'should not allow super admin to delete super admin' do
    sign_in @a1, scope: :admin
    get :destroy, params: { id: @a5 }
    assert_redirected_to root_path
  end

  test 'should allow super admin to delete regular admin' do
    sign_in @u5, scope: :admin
    get :destroy, params: { id: @a6 }
    assert :success
  end
end
# rubocop:enable Metrics/ClassLength
```


### Integration Tests

### Wrapping Up
* Enter the command "git push origin 11-01-admin_show".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* The email display on the admin profile page will be flagged by Hakiri. Log into Hakiri and mark this as a false positive by clicking on Details -> 1 warning -> admin view -> 1 warning -> Mark as False Positive.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
