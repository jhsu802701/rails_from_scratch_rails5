# Unit 10
## Chapter 2: Admin Methods
In this chapter, you will provide admin methods. Only admins should be allowed to view admin profiles or the admin index.  Only super admins should be allowed to delete regular admins.  Super admins should not be able to delete each other or themselves.  (Remember that admins can delete themselves through the registration edit process.)

NOTE: Brace yourself for a long chapter.

### New Branch
Enter the command "git checkout -b 10-02-admin_methods".

### Admin Controller Test
* Enter the command "rails generate controller Admins new".
* Enter the command "rm app/helpers/admins_helper.rb".
* Enter the command "rm app/views/admins/new.html.erb".
* Replace the contents of test/controllers/admins_controller_test.rb with the following:
```
require 'test_helper'

# rubocop:disable Metrics/ClassLength
class UsersControllerTest < ActionController::TestCase
  # PART 1: SHOW
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
  test 'should not allow visitor to delete regular admin' do
    get :destroy, params: { id: @a6 }
    assert_redirected_to root_path
  end

  # NOTE: User can delete self through edit registration form.
  test 'should not allow user to delete self' do
    sign_in @u7, scope: :user
    get :destroy, params: { id: @u7 }
    assert_redirected_to root_path
  end

  test 'should not allow user to delete another user' do
    sign_in @u1, scope: :user
    get :destroy, params: { id: @u7 }
    assert_redirected_to root_path
  end

  test 'should allow super admin to delete user' do
    sign_in @a1, scope: :admin
    get :destroy, params: { id: @u7 }
    assert :success
    assert_redirected_to users_path
  end

  test 'should allow regular admin to delete user' do
    sign_in @a4, scope: :admin
    get :destroy, params: { id: @u7 }
    assert :success
    assert_redirected_to users_path
  end
end
# rubocop:enable Metrics/ClassLength
```

### Admin Show Integration Test

### Admin Index Integration Test

### Admin Delete Integration Test

### Getting the Admin Controller Tests to Pass

### Getting the Integration Tests to Pass

### Wrapping Up
