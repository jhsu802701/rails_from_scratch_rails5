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
```
* Enter the command "sh testc.sh".  All 4 new controller tests will fail because of a missing route.
* Update the routing. Edit the file config/routes.rb and add the following line to the end of the admin section:
```
  resources :admins, only: [:show]
```
* In config/routes.rb, remove the line "get 'admins/new'".  This line is unnecessary, because the general public is not permitted to sign up as an admin.
* Enter the command "sh testc.sh".
* All four new tests fail because the "show" action is missing from the admin controller.
* Replace the contents of the file app/controllers/admins_controller.rb with the following:
```
#
class AdminsController < ApplicationController
  ##############################
  # BEGIN: before_action section
  ##############################
  before_action :may_show_admin, only: [:show]
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
* Enter the command "sh testc.sh".  All 4 controller tests fail because of a missing template.
* Enter the command "touch app/views/admins/show.html.erb".  This creates a blank file that you'll fill in later.
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should now pass, and there should be no offenses.

### Integration Tests
* Enter the command "rails generate integration_test admins_show".
* Replace the contents of the file test/integration/admins_show_test.rb with the following:
```
require 'test_helper'

class AdminsShowTest < ActionDispatch::IntegrationTest
  def check_profile_disabled(a)
    visit admin_path(a)
    assert page.has_css?('title', text: full_title(''),
                                  visible: false)
    assert page.has_css?('h1', text: 'Home', visible: false)
  end

  # rubocop:disable Metrics/AbcSize
  # rubocop:disable Metrics/MethodLength
  def check_own_profile(a)
    login_as(a, scope: :admin)
    visit root_path
    assert page.has_link?('Your Profile', href: admin_path(a))
    click_on 'Logout'
  end

  def check_profile_enabled(a)
    fn = a.first_name
    ln = a.last_name
    un = a.username
    e = a.email
    visit admin_path(a)
    assert page.has_css?('title', text: full_title("Admin: #{fn} #{ln}"),
                                  visible: false)
    assert page.has_css?('h1', text: "Admin: #{fn} #{ln}",
                               visible: false)
    assert page.has_text?("Username: #{un}")
    assert page.has_text?("Email: #{e}")
  end
  # rubocop:enable Metrics/AbcSize
  # rubocop:enable Metrics/MethodLength

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
    check_profile_enabled(@a1)
    check_profile_enabled(@a2)
    check_profile_enabled(@a3)
    check_profile_enabled(@a4)
    check_profile_enabled(@a5)
    check_profile_enabled(@a6)
  end

  test 'super admin can view all admin profiles' do
    login_as(@a1, scope: :admin)
    check_profile_enabled(@a1)
    check_profile_enabled(@a2)
    check_profile_enabled(@a3)
    check_profile_enabled(@a4)
    check_profile_enabled(@a5)
    check_profile_enabled(@a6)
  end

  test 'admins can access their own profiles from the menu bar' do
    check_own_profile(@a1)
    check_own_profile(@a2)
    check_own_profile(@a3)
    check_own_profile(@a4)
    check_own_profile(@a5)
    check_own_profile(@a6)
  end  
end
```
* Enter the command "sh build_fast.sh".  All 3 new integration tests will fail because of missing content.
* Enter the command "alias test1='(command for repeating the failed tests minus the TESTOPTS portion)'".
* Enter the command "test1". This runs only the tests in test/integration/admins_show_test.rb. All 3 integration tests will fail.
* Create the file app/views/admins/show.html.erb with the following content:
```
<% require 'email_munger' %>

<% provide(:title, "Admin: #{@admin.first_name} #{@admin.last_name}") %>
<div class="row">
  <section class="user_info">
    <h1>
    Admin: <%= @admin.first_name %> <%= @admin.last_name %>
    </h1>
    Username: <%= @admin.username %>
    <br>
    Email: <%= raw(EmailMunger.encode(@admin.email)) %>
    <br>
    <% if @admin.super != true && current_admin.super == true %>
      <%= link_to "Delete", @admin, method: :delete,
                            data: { confirm: "Are you sure you wish to delete this admin?" },
                            class: "btn btn-lg btn-primary"
      %>
    <% end %>
  </section>
</div>
```
* Enter the command "test1".  All 3 integration tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

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
