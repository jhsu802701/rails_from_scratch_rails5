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
* Enter the command "sh testc.sh".

### Admin Show Integration Test
* Enter the command "rails generate integration_test users_show".
* Replace the contents of the file test/integration/admins_show_test.rb with the following:
```
require 'test_helper'

class AdminsShowTest < ActionDispatch::IntegrationTest
  def check_profile_disabled(a)
    visit admin_path(u)
    assert page.has_css?('title', text: full_title(''),
                                  visible: false)
    assert page.has_css?('h1', text: 'Home', visible: false)
  end

  # rubocop:disable Metrics/AbcSize
  # rubocop:disable Metrics/MethodLength
  def check_page(a)
    fn = a.first_name
    ln = a.last_name
    un = a.username
    e = a.email
    visit admin_path(a)
    assert page.has_css?('title', text: full_title("User: #{fn} #{ln}"),
                                  visible: false)
    assert page.has_css?('h1', text: "User: #{fn} #{ln}",
                               visible: false)
    assert page.has_text?("Username: #{un}")
    assert page.has_text?("Email: #{e}")
  end
  # rubocop:enable Metrics/AbcSize
  # rubocop:enable Metrics/MethodLength

  def check_own_page(a)
    login_as(a, scope: :admin)
    check_page(a)
  end

  test 'unregistered visitors may not view admin profile pages' do
    check_profile_disabled(@a1)
    check_profile_disabled(@a2)
    check_profile_disabled(@a3)
    check_profile_disabled(@a4)
    check_profile_disabled(@a5)
    check_profile_disabled(@a6)
  end

  test 'user may not view admin profile pages' do
    login_as(@u1, scope: :user)
    check_profile_disabled(@a1)
    check_profile_disabled(@a2)
    check_profile_disabled(@a3)
    check_profile_disabled(@a4)
    check_profile_disabled(@a5)
    check_profile_disabled(@a6)
  end

  test 'regular admin can view all admin profiles' do
    login_as(@a4, scope: :admin)
    check_page(@a1)
    check_page(@a2)
    check_page(@a3)
    check_page(@a4)
    check_page(@a5)
    check_page(@a6)
  end

  test 'super admin can view all admin profiles' do
    login_as(@a1, scope: :admin)
    check_page(@a1)
    check_page(@a2)
    check_page(@a3)
    check_page(@a4)
    check_page(@a5)
    check_page(@a6)
  end
end
```


### Admin Index Integration Test

### Admin Delete Integration Test

### Getting the Admin Controller Tests to Pass

### Getting the Integration Tests to Pass

### Wrapping Up
