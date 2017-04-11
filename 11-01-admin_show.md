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
  ###########
  # END: show
  ###########
  ##############
  # BEGIN: index
  ##############

  ############
  # END: index
  ############
  ###############
  # BEGIN: delete
  ###############

  #############
  # END: delete
  #############
end
# rubocop:enable Metrics/ClassLength
```
* Update the routing. Edit the file config/routes.rb and add the following line to the end of the admin section:
```
  resources :admins, only: [:show]
```
* Enter the command "sh testc.sh".
* All four new tests fail because the "show" action is missing from the admin controller.
* Replace the contents of the file app/controllers/admins_controller.rb with the following:
```
#
class AdminsController < ApplicationController
  ##############################
  # BEGIN: before_action section
  ##############################
  before_action :may_show_admin, only: [:index, :show]
  before_action :may_destroy_admin, only: [:destroy]
  ############################
  # END: before_action section
  ############################

  #######################
  # BEGIN: action section
  #######################
  def show
    @admin = Admin.find(params[:id])
  end
  #####################
  # END: action section
  #####################

  private

  ########################
  # BEGIN: private section
  ########################
  def may_show_admin
    return redirect_to(root_path) unless admin_signed_in? == true
  end
  helper_method :may_show_admin
  ######################
  # END: private section
  ######################
end
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
