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

```

### User Index Integration Test
* Enter the command "rails generate integration_test users_index".
* Replace the contents of the file test/integration/users_index_test.rb with the following:
```

```

### User Delete Integration Test
* Enter the command "rails generate integration_test users_delete".
* Replace the contents of the file test/integration/users_delete_test.rb with the following:
```
```

### test/test_helper.rb
* Add the following lines to the file test/test_helper.rb before the Capybara section:
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
* This section ensures that all controller tests in this app automatically include the devise test controller helpers, the setup_universal actions, and the teardown_universal actions.
* Enter the command "sh testc.sh".  All 4 user controller tests will still fail because the route is missing.

### Routing
* In the user section of config/routes.rb, add the following line:
```
resources :users, only: [:show]
```
* Enter the command "sh testc.sh".  All 4 user controller tests will still fail because the show action is not found.

### User Controller
* Replace the contents of the file app/controllers/users_controller.rb with the following:
```
#
class UsersController < ApplicationController
  before_action :may_show_user, only: [:show]

  def show
    @user = User.find(params[:id])
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
