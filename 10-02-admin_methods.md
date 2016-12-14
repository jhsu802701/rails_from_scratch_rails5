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
class AdminsControllerTest < ActionController::TestCase
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
* Enter the command "sh testc.sh".  All 16 new controller tests will fail.

### Admin Show Integration Test
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
* Enter the command "alias test1='rails test test/integration/admins_show_test.rb'".
* Enter the command "test1". This runs only the tests in test/integration/admins_show_test.rb.  All 4 integration tests will fail.

### Admin Index Integration Test
* Enter the command "rails generate integration_test admins_index".
* Replace the contents of the file test/integration/admins_index_test.rb with the following:
```
require 'test_helper'

class AdminsIndexTest < ActionDispatch::IntegrationTest
  def check_index_disabled
    visit admins_path
    assert page.has_css?('title', text: full_title(''),
                                  visible: false)
    assert page.has_css?('h1', text: 'Home', visible: false)
  end

  def check_index_disabled_for_user(u)
    login_as(u, scope: :user)
    check_index_disabled
  end

  # rubocop:disable Metrics/AbcSize
  # rubocop:disable Metrics/MethodLength
  def check_index
    visit admins_path
    assert page.has_css?('title', text: full_title('Admin Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Admin Index')
    assert page.has_text?('Woods')
    assert page.has_text?('Kensington')
    assert page.has_text?('Richmond')
    assert page.has_text?('Bonafonte')
    assert page.has_text?('Callahan')
    assert page.has_text?('Huntington')

    # Verify that index page provides access to profile pages
    assert page.has_link?('ewoods', href: user_path(@a1))
    assert page.has_link?('vkensington', href: user_path(@a2))
    assert page.has_link?('erichmond', href: user_path(@a3))
    assert page.has_link?('pbonafonte', href: user_path(@a4))
    assert page.has_link?('pcallahan', href: user_path(@a5))
    assert page.has_link?('whuntington', href: user_path(@a6))

    # Verify that root page provides access to index page
    click_on 'Home'
    assert page.has_link?('Admin Index', href: admins_path)
  end
  # rubocop:enable Metrics/AbcSize
  # rubocop:enable Metrics/MethodLength

  test 'admins index page is not accessible to visitors' do
    check_index_disabled
  end

  test 'admins index page is not accessible to users' do
    check_index_disabled_for_user(@a1)
    check_index_disabled_for_user(@a2)
    check_index_disabled_for_user(@a3)
    check_index_disabled_for_user(@a4)
    check_index_disabled_for_user(@a5)
    check_index_disabled_for_user(@a6)
  end

  test 'users index page is accessible to regular admins' do
    login_as(@a4, scope: :admin)
    check_index
  end

  test 'admins index page is accessible to super admins' do
    login_as(@a1, scope: :admin)
    check_index
  end
end
```
* Enter the command "alias test2='rails test test/integration/admins_index_test.rb'".
* Enter the command "test2". This tests only the tests in test/integration/admins_index_test.rb.  All 4 integration tests will fail.

### Admin Delete Integration Test
* Enter the command "rails generate integration_test admins_delete".
* Replace the contents of the file test/integration/admins_delete_test.rb with the following:
```
require 'test_helper'

class AdminsDeleteTest < ActionDispatch::IntegrationTest
  test 'regular admin does not get button to delete self' do
    login_as(@a6, scope: :admin)
    visit admin_path(@a6)
    assert page.has_no_link?('Delete', href: admin_path(@a6))
  end

  test 'super admin does not get button to delete self' do
    login_as(@a5, scope: :admin)
    visit admin_path(@a5)
    assert page.has_no_link?('Delete', href: admin_path(@a5))
  end

  test 'regular admin does not get button to delete super admin' do
    login_as(@a6, scope: :admin)
    visit admin_path(@a5)
    assert page.has_no_link?('Delete', href: admin_path(@a5))
  end

  test 'super admin can delete regular admin' do
    login_as(a5, scope: :admin)
    visit root_path
    click_on 'Admin Index'
    assert_difference 'Admin.count', -1 do
      click_on 'whuntington'
      click_on 'Delete'
    end
    assert_text 'Admin deleted'
  end
end
```
* Enter the command "alias test3='rails test test/integration/admins_delete_test.rb'".
* Enter the command "test3". All 4 tests will fail.

### Getting the Admin Controller Tests to Pass
* Enter the command "sh testc.sh".  All 16 tests will fail because the expected routes are not present.
* Update the routing.  Edit the file config/routes.rb and add the following line to the end of the admin section:
```
  resources :admins, only: [:show, :index, :destroy]
```
* In the config/routes.rb file, remove the line "get 'admins/new'", because this capability is not used in this app.
* Enter the command "sh testc.sh".  All 16 tests still fail.  Some fail because the show action is not available, some fail because the index action is not available, and some fail because the destroy route is not available.
* Replace the contents of the file app/controllers/admins_controller.rb with the following:
```
#
class AdminsController < ApplicationController
  before_action :may_show_admin, only: [:index, :show]
  before_action :may_destroy_admin, only: [:destroy]

  def index
    @admins = Admin.paginate(page: params[:page])
  end

  def show
    @admin = Admin.find(params[:id])
  end

  def destroy
    Admin.find(params[:id]).destroy
    flash[:success] = 'Admin deleted'
    redirect_to(admins_path)
  end

  private

  def may_show_admin
    return redirect_to(root_path) unless admin_signed_in? == true
  end
  helper_method :may_show_admin

  def no_destroy
    ta = Admin.find(params[:id]) # Target admin
    ca = current_admin
    # Do not delete if:
    # 1.  current_admin is nil OR
    # 2.  Attempting to delete self OR
    # 3.  Not a super admin OR
    # 4.  Target is super admin
    ca.nil? || ca == ta || ca.super != true || ta.super == true
  end

  def may_destroy_admin
    return redirect_to(root_path) if no_destroy == true
  end
  helper_method :may_destroy_admin
end
```
* Enter the command "sh testc.sh".
* Create the file app/views/admins/show.html.erb with the following content:
```
<% require 'email_munger' %>

<% provide(:title, "Admin: #{@admin.first_name} #{@admin.last_name}") %>
<div class="row">
  <aside class="col-md-4">
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
  </aside>
</div>
```
* Enter the command "sh testc.sh".
* Create the file app/views/admins/index.html.erb with the following content:
```
<% provide(:title, 'Admin Index') %>

<h1>Admin Index</h1>

<table class="admins">
  <tr>
    <td><b>Last Name</b></td>
    <td><b>First Name</b></td>
    <td><b>Super?</b></td>
    <td><b>Username</b></td>
  </tr>
  <%= render @admins %>
</table>
<%= will_paginate %>
```
* Create the file app/views/admins/_admin.html.erb with the following content to show information on each admin in the index:
```
<% require 'email_munger' %>
<tr>
  <td><%= admin.last_name %></td>
  <td><%= admin.first_name %></td>
  <td>
  <% if admin.super == true  %>
    Y
  <% else %>
    N
  <% end %>
  </td>
  <td><%= link_to admin.username, admin %></td>
</tr>
```
* Enter the command "sh testc.sh". All of the controller tests should now pass.

### Getting the Integration Tests to Pass
* Enter the command "test1".
* Enter the command "test2".
* In the admin section in app/views/layouts/_header.html.erb, add the following line just after the one containing "User Index":
```
<li><%= link_to "Admin Index",   admins_path %></li>
```
* Enter the command "test2".
* Enter the command "test3".

### Rails Best Practices

### Wrapping Up
* Enter the command "git push origin 10-02-admin_methods".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* The email display on the admin profile page will be flagged by Hakiri. Mark this as a false positive by clicking on Details -> 1 warning -> admin view -> 1 warning -> Mark as False Positive.
* The dynamic render path in the admin index page will be flagged by Hakiri. Mark this issue as a false positive as well.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
