# Unit 10
## Chapter 2: Showing Admins

### New Branch
Enter the command "git checkout -b 10-02-admins_show".

### Admin Controller Test
* Enter the command "rails generate controller Admins new".
* Enter the command "rm app/helpers/admins_helper.rb".
* Enter the command "rm app/views/admins/new.html.erb".
* Replace the contents of test/controllers/admins_controller_test.rb with the following:
```
require 'test_helper'

class AdminsControllerTest < ActionController::TestCase
  test 'should redirect profile page when not logged in' do
    get :show, params: { id: @a1 }
    assert_redirected_to root_path
    get :show, params: { id: @a2 }
    assert_redirected_to root_path
    get :show, params: { id: @a3 }
    assert_redirected_to root_path
    get :show, params: { id: @a4 }
    assert_redirected_to root_path
  end

  test 'should redirect profile page when logged in as a user' do
    sign_in users(:connery)

    get :show, params: { id: @a1 }
    assert_redirected_to root_path
    get :show, params: { id: @a2 }
    assert_redirected_to root_path
    get :show, params: { id: @a3 }
    assert_redirected_to root_path
    get :show, params: { id: @a4 }
    assert_redirected_to root_path
  end

  test 'should not redirect profile page when logged in as a super admin' do
    sign_in admins(:elle_woods)

    # Self
    get :show, params: { id: @a1 }
    assert :success

    # Others
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

  test 'should not redirect profile page when logged in a regular admin' do
    sign_in admins(:emmett_richmond)

    # Self
    get :show, params: { id: @a3 }
    assert :success

    # Others
    get :show, params: { id: @a1 }
    assert :success
    get :show, params: { id: @a2 }
    assert :success
    get :show, params: { id: @a4 }
    assert :success
    get :show, params: { id: @a5 }
    assert :success
    get :show, params: { id: @a6 }
    assert :success
  end
end
```
* Enter the command "sh testc.sh".  All 4 of the new admin tests will fail because the routing is missing.

### Routing
* In the admin section of config/routes.rb, add the following line:
```
resources :admins, only: [:show]
```
* Enter the command "sh testc.sh". All 4 admin controller tests will still fail because the show action is not found.

### Admin Controller
* Replace the contents of the file app/controllers/admins_controller.rb with the following:
```
#
class AdminsController < ApplicationController
  before_action :may_show_admin, only: [:index]

  def show
    @admin = Admin.find(params[:id])
  end

  private

  def may_show_admin
    return redirect_to(root_path) unless admin_signed_in? == true
  end

  helper_method :may_show_admin
end
```
* Enter the command "sh testc.sh". The first two of the new admin controller tests will pass, but the other two will still fail due to missing admin profile pages.

### Admin Profile Page
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
* Enter the command "sh testc.sh". Now all of the controller tests should pass.
* Enter the command "sh testcl.sh". All controller tests should pass, and there should be no flagged issues.
* Enter the command "sh git_check.sh". All tests should pass, and there should be no flagged issues.
* Enter the following commands:
```
git add .
git commit -m "Added admin show page"
```

### Wrapping Up
* Enter the command "git push origin 10-02-admins_show".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* The email display on the admin profile page will be flagged by Hakiri. Mark this as a false positive by clicking on Details -> 1 warning -> admin view -> 1 warning -> Mark as False Positive.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
