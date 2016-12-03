# Unit 10
## Chapter 1: User Methods

In this chapter, you will provide user profiles.  The only people who should be allowed to view a user profile are that specific user and the admins.

### New Branch
Enter the command "git checkout -b 10-01-user_methods".

### User Controller Test
* Enter the command "rails generate controller Users new".
* Enter the command "rm app/helpers/users_helper.rb".
* Enter the command "rm app/views/users/new.html.erb".
* Replace the contents of test/controllers/users_controller_test.rb with the following:
```
require 'test_helper'

# rubocop:disable Metrics/ClassLength
class UsersControllerTest < ActionController::TestCase
  # PART 1: SHOW
  test 'should redirect profile page when not logged in' do
    get :show, params: { id: @u1 }
    assert_redirected_to root_path
    get :show, params: { id: @u2 }
    assert_redirected_to root_path
    get :show, params: { id: @u3 }
    assert_redirected_to root_path
    get :show, params: { id: @u4 }
    assert_redirected_to root_path
    get :show, params: { id: @u5 }
    assert_redirected_to root_path
    get :show, params: { id: @u6 }
    assert_redirected_to root_path
    get :show, params: { id: @u7 }
    assert_redirected_to root_path
  end

  test 'should redirect users from profiles other than their own' do
    sign_in users(:connery)

    # Self
    get :show, params: { id: @u1 }
    assert :success

    # Others
    get :show, params: { id: @u2 }
    assert_redirected_to root_path
    get :show, params: { id: @u3 }
    assert_redirected_to root_path
    get :show, params: { id: @u4 }
    assert_redirected_to root_path
    get :show, params: { id: @u5 }
    assert_redirected_to root_path
    get :show, params: { id: @u6 }
    assert_redirected_to root_path
    get :show, params: { id: @u7 }
    assert_redirected_to root_path
  end

  test 'should not redirect profile page when logged in as a super admin' do
    sign_in admins(:elle_woods)
    get :show, params: { id: @u1 }
    assert :success
    get :show, params: { id: @u2 }
    assert :success
    get :show, params: { id: @u3 }
    assert :success
    get :show, params: { id: @u4 }
    assert :success
    get :show, params: { id: @u5 }
    assert :success
    get :show, params: { id: @u6 }
    assert :success
    get :show, params: { id: @u7 }
    assert :success
  end

  test 'should not redirect profile page when logged in as a regular admin' do
    sign_in admins(:emmett_richmond)
    get :show, params: { id: @u1 }
    assert :success
    get :show, params: { id: @u2 }
    assert :success
    get :show, params: { id: @u3 }
    assert :success
    get :show, params: { id: @u4 }
    assert :success
    get :show, params: { id: @u5 }
    assert :success
    get :show, params: { id: @u6 }
    assert :success
    get :show, params: { id: @u7 }
    assert :success
  end

  # PART 2: INDEX
  test 'should redirect index page when not logged in' do
    get :index
    assert_redirected_to root_path
  end

  test 'should redirect index page when logged in as a user' do
    sign_in users(:elle_woods)
    get :index
    assert_redirected_to root_path
  end

  test 'should not redirect index page when logged in as a super admin' do
    sign_in admins(:elle_woods)
    get :index
    assert :success
  end

  test 'should not redirect index page when logged in as a regular admin' do
    sign_in admins(:emmett_richmond)
    get :index
    assert :success
  end

  # PART 3: DELETE
  test 'should not allow visitor to delete user' do
    get :destroy, params: { id: @u7 }
    assert_redirected_to root_path
  end

  # NOTE: User can delete self through edit registration form.
  test 'should not allow user to delete self' do
    sign_in :user, @u7
    get :destroy, params: { id: @u7 }
    assert_redirected_to root_path
  end

  test 'should not allow user to delete another user' do
    sign_in :user, @u1
    get :destroy, params: { id: @u7 }
    assert_redirected_to root_path
  end

  test 'should allow super admin to delete user' do
    sign_in admins(:elle_woods)
    get :destroy, params: { id: @u7 }
    assert :success
    assert_redirected_to root_path
  end

  test 'should allow regular admin to delete user' do
    sign_in admins(:emmett_richmond)
    get :destroy, params: { id: @u7 }
    assert :success
    assert_redirected_to root_path
  end
end
# rubocop:enable Metrics/ClassLength
```
* Enter the command "sh testc.sh".  All 13 new user controller tests will fail because of the unexpected method "sign_in".

### User Show Integration Test
* Enter the command "rails generate integration_test users_show".
* Replace the contents of the file test/integration/users_show_test.rb with the following:
```
require 'test_helper'
require 'email_munger'

class UsersShowTest < ActionDispatch::IntegrationTest
  def test_profile_disabled(u)
    visit user_path(u)
    assert page.has_css?('title', text: full_title('Home'),
                                  visible: false)
  end

  def user_view_other_profile(u1, u2)
    login_as(u1, scope: :user)
    visit test_profile_disabled(u2)
  end

  # rubocop:disable Metrics/AbcSize
  # rubocop:disable Metrics/MethodLength
  def check_page(u)
    fn = u.first_name
    ln = u.last_name
    un = u.username
    e = EmailMunger.encode(u.email)
    visit user_path(u)
    assert page.has_css?('title', text: full_title("User: #{fn} #{ln}"),
                                  visible: false)
    assert page.has_css?('h1', text: "User: #{fn} #{ln}",
                               visible: false)
    assert page.has_text?("Username: #{un}")
    assert page.has_text?("Email: #{e}")
  end
  # rubocop:enable Metrics/AbcSize
  # rubocop:enable Metrics/MethodLength

  def check_own_page(u)
    login_as(u, scope: :user)
    check_page(u)
  end

  test 'unregistered visitors may not view user profile pages' do
    test_profile_disabled(@u1)
    test_profile_disabled(@u2)
    test_profile_disabled(@u3)
    test_profile_disabled(@u4)
    test_profile_disabled(@u5)
    test_profile_disabled(@u6)
    test_profile_disabled(@u7)
  end

  test 'user may not view profiles of other users' do
    user_view_other_profile(@u1, @u2)
    user_view_other_profile(@u1, @u3)
    user_view_other_profile(@u1, @u4)
    user_view_other_profile(@u1, @u5)
    user_view_other_profile(@u1, @u6)
    user_view_other_profile(@u1, @u7)
  end

  test 'users can view their own profiles' do
    check_own_page(@u1)
    check_own_page(@u2)
    check_own_page(@u3)
    check_own_page(@u4)
    check_own_page(@u5)
    check_own_page(@u6)
    check_own_page(@u7)
  end

  test 'super admin can view user profile' do
    login_as(@a1, scope: :admin)
    check_page(u1)
    check_page(u2)
    check_page(u3)
    check_page(u4)
    check_page(u5)
    check_page(u6)
    check_page(u7)
  end

  test 'regular admin can view user profiles' do
    login_as(@a4, scope: :admin)
    check_page(u1)
    check_page(u2)
    check_page(u3)
    check_page(u4)
    check_page(u5)
    check_page(u6)
    check_page(u7)
  end
end
```

### User Index Integration Test
* Enter the command "rails generate integration_test users_index".
* Replace the contents of the file test/integration/users_index_test.rb with the following:
```
require 'test_helper'

class UsersIndexTest < ActionDispatch::IntegrationTest
  def check_index_disabled
    visit users_path
    assert page.has_css?('title', text: full_title('Home'),
                                  visible: false)
  end

  def check_index_disabled_for_user(u)
    login_as(u, scope: :user)
    check_index_disabled
  end

  # rubocop:disable Metrics/AbcSize
  # rubocop:disable Metrics/MethodLength
  def check_index
    visit users_path
    assert page.has_css?('title', text: full_title('User Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'User Index')
    assert page.has_text?('Connery')
    assert page.has_text?('Lazenby')
    assert page.has_text?('Moore')
    assert page.has_text?('Dalton')
    assert page.has_text?('Brosnan')
    assert page.has_text?('Craig')
    assert page.has_text?('Blofeld')

    # Verify that index page provides access to profile pages
    assert page.has_link?('sconnery', href: user_path(@u1))
    assert page.has_link?('glazenby', href: user_path(@u2))
    assert page.has_link?('rmoore', href: user_path(@u3))
    assert page.has_link?('tdalton', href: user_path(@u4))
    assert page.has_link?('pbrosnan', href: user_path(@u5))
    assert page.has_link?('dcraig', href: user_path(@u6))
    assert page.has_link?('eblofeld', href: user_path(@u7))

    # Verify that root page provides access to index page
    click_on 'Home'
    assert page.has_link?('User Index', href: users_path)
  end
  # rubocop:enable Metrics/AbcSize
  # rubocop:enable Metrics/MethodLength

  test 'users index page is not accessible to visitors' do
    check_index_disabled
  end

  test 'users index page is not accessible to users' do
    check_index_disabled_for_user(@u1)
    check_index_disabled_for_user(@u2)
    check_index_disabled_for_user(@u3)
    check_index_disabled_for_user(@u4)
    check_index_disabled_for_user(@u5)
    check_index_disabled_for_user(@u6)
    check_index_disabled_for_user(@u7)
  end

  test 'users index page is accessible to super admins' do
    login_as(@a1, scope: :admin)
    check_index
  end

  test 'users index page is accessible to regular admins' do
    login_as(@a4, scope: :admin)
    check_index
  end
end
```

### User Delete Integration Test
* Enter the command "rails generate integration_test users_delete".
* Replace the contents of the file test/integration/users_delete_test.rb with the following:
```
require 'test_helper'

class UsersDeleteTest < ActionDispatch::IntegrationTest
  def delete_user(u)
    visit user_path(u)
    assert_difference 'User.count', -1 do
      click_on 'Delete'
    end
  end

  def check_delete(a)
    login_as(a, scope: :admin)
    delete_user(@u1)
    delete_user(@u2)
    delete_user(@u3)
    delete_user(@u4)
    delete_user(@u5)
    delete_user(@u6)
    delete_user(@u7)
  end

  test 'user does not get button to delete self' do
    login_as(@u1, scope: :user)
    visit user_path(@u1)
    assert page.has_no_link?('Delete', href: user_path(@u1))
  end

  test 'super admin gets button to delete user' do
    check_delete(@a1)
  end

  test 'regular admin gets button to delete user' do
    check_delete(@a4)
  end
end
```

### Getting the User Controller Tests to Pass
* Automatically provide Devise test controller helpers, the setup_universal actions, and the teardown_universal actions to all controller tests.  Edit the file test/test_helper.rb and add the following lines before the Capybara section:
```
##############################
# BEGIN: controller test setup
##############################
class ActionController::TestCase
  include Devise::Test::ControllerHelpers

  # Execute before each integration test
  def setup
    setup_universal
  end

  # Execute after each integration test
  def teardown
    teardown_universal
  end
end
############################
# END: controller test setup
############################
```
* Update the routing.  Edit the file config/routes.rb and add the following lines to the user section:
```
  resources :users, only: [:show, :index, :delete]
  resources :users do
    collection { post :search, to: 'users#index' }
  end
```
* Add the following line to the end of the Gemfile:
```
# Pagination gems
gem 'will_paginate' # For pagination
gem 'bootstrap-will_paginate' # Twitter Bootstrap for pagination
```

* Replace the contents of the file app/controllers/users_controller.rb with the following:
```
#
class UsersController < ApplicationController
  before_action :may_show_user, only: [:show]
  before_action :may_index_user, only: [:index]
  before_action :may_destroy_user, only: [:destroy]

  def index
    @users = User.paginate(page: params[:page])
  end

  def show
    @search = User.search(params[:q])
    @users = @search.result.paginate(page: params[:page])
    @search.build_condition if @search.conditions.empty?
    @search.build_sort if @search.sorts.empty?
  end

  def destroy
    User.find(params[:id]).destroy
    flash[:success] = 'User deleted'
    redirect_to(users_path)
  end

  private

  def admin_or_correct_user
    current_user == User.find(params[:id]) || admin_signed_in?
  end
  helper_method :admin_or_correct_user

  def may_show_user
    return redirect_to(root_path) unless admin_or_correct_user
  end
  helper_method :may_show_user

  def may_index_user
    return redirect_to(root_path) unless admin_signed_in?
  end
  helper_method :may_index_user

  def may_destroy_user
    return redirect_to(root_path) unless admin_signed_in?
  end
  helper_method :may_destroy_user
end
```
* Enter the command "sh testc.sh".  The first of the new user controller tests will pass, but the other three will still fail due to missing user profile pages.  

### User Profile Page
* Create the file app/views/users/show.html.erb with the following content:
```
<% require 'email_munger' %>
<% provide(:title, "User: #{@user.first_name} #{@user.last_name}") %>

<div class="row">
  <aside class="col-md-4">
    <section class="user_info">
    <h1>
    User: <%= @user.first_name %> <%= @user.last_name %>
    </h1>
    Username: <%= @user.username %>
    <br>
    Email: <%= raw(EmailMunger.encode(@user.email)) %>
    <br>
    </section>
  </aside>
</div>
```
* Enter the command "sh testc.sh".  Now all of the controller tests should pass.
* Enter the command "sh testcl.sh".  All controller tests should pass, and there should be no flagged issues.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no flagged issues.
* Enter the following commands:
```
git add .
git commit -m "Added user show page"
git push origin master
```

### User Index Page

### User Delete Button

### Wrapping Up
* Enter the command "git push origin 10-01-user_methods".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* The email display on the user profile page will be flagged by Hakiri.  Mark this as a false positive by clicking on Details -> 1 warning -> admin view -> 1 warning -> Mark as False Positive.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
