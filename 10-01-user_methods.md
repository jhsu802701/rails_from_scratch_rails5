# Unit 10
## Chapter 1: User Methods

In this chapter, you will provide user methods.  The only people who should be allowed to view a user profile are that specific user and the admins.  Users should not be able to view the profiles of other users or the user index and should not be able to delete themselves or other users.  (However, users can delete themselves through the registration edit process.)  Admins should have access to all user profiles and the user index, and admins should be able to delete users.

NOTE: Brace yourself for a long chapter.

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
    sign_in @u1, scope: :user

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
    sign_in @a1, scope: :admin
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
    sign_in @a4, scope: :admin
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
  test 'should not allow visitor to delete user' do
    get :destroy, params: { id: @u7 }
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
* Enter the command "sh testc.sh".  All 13 new user controller tests will fail due to the undefined method sign_in or due to missing routes.

### User Show Integration Test
* Enter the command "rails generate integration_test users_show".
* Replace the contents of the file test/integration/users_show_test.rb with the following:
```
require 'test_helper'

class UsersIndexTest < ActionDispatch::IntegrationTest
  def check_index_disabled
    visit users_path
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
  def check_index_enabled
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
    assert page.has_link?('sean_connery@example.com', href: user_path(@u1))
    assert page.has_link?('glazenby', href: user_path(@u2))
    assert page.has_link?('george_lazenby@example.com', href: user_path(@u2))
    assert page.has_link?('rmoore', href: user_path(@u3))
    assert page.has_link?('roger_moore@example.com', href: user_path(@u3))
    assert page.has_link?('tdalton', href: user_path(@u4))
    assert page.has_link?('timothy_dalton@example.com', href: user_path(@u4))
    assert page.has_link?('pbrosnan', href: user_path(@u5))
    assert page.has_link?('pierce_brosnan@example.com', href: user_path(@u5))
    assert page.has_link?('dcraig', href: user_path(@u6))
    assert page.has_link?('daniel_craig@example.com', href: user_path(@u6))
    assert page.has_link?('eblofeld', href: user_path(@u7))
    assert page.has_link?('ernst_blofeld@example.com', href: user_path(@u7))

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
    check_index_enabled
  end

  test 'users index page is accessible to regular admins' do
    login_as(@a4, scope: :admin)
    check_index_enabled
  end
end
```
* Enter the command "alias test1='rails test test/integration/users_show_test.rb'".
* Enter the command "test1".  This runs only the tests in test/integration/users_show_test.rb.  All 5 of the tests will fail.

### User Index Integration Test
* Enter the command "rails generate integration_test users_index".
* Replace the contents of the file test/integration/users_index_test.rb with the following:
```
require 'test_helper'

class UsersIndexTest < ActionDispatch::IntegrationTest
  def check_index_disabled
    visit users_path
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
  def check_index_enabled
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
    assert page.has_link?('sean_connery@example.com', href: user_path(@u1))
    assert page.has_link?('glazenby', href: user_path(@u2))
    assert page.has_link?('george_lazenby@example.com', href: user_path(@u2))
    assert page.has_link?('rmoore', href: user_path(@u3))
    assert page.has_link?('roger_moore@example.com', href: user_path(@u3))
    assert page.has_link?('tdalton', href: user_path(@u4))
    assert page.has_link?('timothy_dalton@example.com', href: user_path(@u4))
    assert page.has_link?('pbrosnan', href: user_path(@u5))
    assert page.has_link?('pierce_brosnan@example.com', href: user_path(@u5))
    assert page.has_link?('dcraig', href: user_path(@u6))
    assert page.has_link?('daniel_craig@example.com', href: user_path(@u6))
    assert page.has_link?('eblofeld', href: user_path(@u7))
    assert page.has_link?('ernst_blofeld@example.com', href: user_path(@u7))

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
    check_index_enabled
  end

  test 'users index page is accessible to regular admins' do
    login_as(@a4, scope: :admin)
    check_index_enabled
  end
end
```
* Enter the command "alias test2='rails test test/integration/users_index_test.rb'".
* Enter the command "test2".  This tests only the tests in test/integration/users_index_test.rb.  All 4 tests will fail.

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
    assert_text 'User deleted'
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
* Enter the command "alias test3='rails test test/integration/users_delete_test.rb'".
* Enter the command "test3".  All 3 tests will fail.

### Getting the User Controller Tests to Pass
* Enter the command "sh testc.sh".  Several of the tests will fail due to the undefined method sign_in, and others will fail due to missing routes.
* Automatically provide Devise test controller helpers (which provide the method sign_in), the setup_universal actions, and the teardown_universal actions to all controller tests.  Edit the file test/test_helper.rb and add the following lines before the Capybara section:
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
* Enter the command "sh testc.sh".  13 tests still fail, and all of these failures are due to missing routes.
* Update the routing.  Edit the file config/routes.rb and add the following lines to the end of the user section:
```
  resources :users, only: [:show, :index, :destroy]
  resources :users do
    collection { post :search, to: 'users#index' }
  end
```
* In the config/routes.rb file, remove the line "get 'users/new'".  This is covered by the Devise registration feature.
* Enter the command "sh testc.sh".  Now the 13 tests fail due to missing actions.
* Replace the contents of the file app/controllers/users_controller.rb with the following:
```
#
class UsersController < ApplicationController
  before_action :may_show_user, only: [:show]
  before_action :may_index_user, only: [:index]
  before_action :may_destroy_user, only: [:destroy]

  def show
    @user = User.find(params[:id])
  end

  # rubocop:disable Metrics/AbcSize
  def index
    @search = User.search(params[:q].presence)
    @users = @search.result.paginate(page: params[:page])
    # NOTE: The following line specifies the sort order.
    # This is reflected in the default sort criteria shown.
    # The user is free to remove these default criteria.
    @search.sorts = 'last_name asc' if @search.sorts.empty?
    @search.build_condition if @search.conditions.empty?
    @search.build_sort if @search.sorts.empty?
    @users = @search.result
    @users = @users.order('last_name').page(params[:page])
  end
  # rubocop:enable Metrics/AbcSize

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
* Enter the command "sh testc.sh".  5 failures remain.  3 of them are due to missing templates for the show action, and tackling this issue is the next step.  (The other 2 are due to the undefined method search.  You will take care of this issue later.)
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
    <% if admin_signed_in? %>
      <%= link_to "Delete #{@user.first_name} #{@user.last_name} (#{@user.username})", @user,
        class: "btn btn-primary", method: :delete,
        data: { confirm: "Are you sure you wish to delete #{@user.first_name} #{@user.last_name}?" } %>
    <% end %>
    </section>
  </aside>
</div>
```
* Enter the command "sh testc.sh".  2 failures remain, and both are due to the undefined method search.
* Add the following line to the end of the Gemfile:
```
# Pagination gems
gem 'bootstrap-will_paginate' # Twitter Bootstrap for pagination
gem 'will_paginate' # For pagination

gem 'ransack' # For searching users
```
* Enter the command "bundle install" to install these new gems.
* Create the file app/views/users/index.html.erb with the following content:
```
<% provide(:title, 'User Index') %>

<br><br>

<%= search_form_for @search, url: search_users_path, method: :post do |f| %>
  <%= f.condition_fields do |c| %>
    <%= render "condition_fields", f: c %>
  <% end %>
  <p><%= link_to_add_fields "Add Conditions", f, :condition %></p>
  <div class="field">
    Sort:
    <%= f.sort_fields do |s| %>
      <%= s.sort_select %>
    <% end %>
  </div>
  <div class="actions"><%= f.submit "Search" %></div>
<% end %>

<h1>User Index</h1>
<%= will_paginate %>
<table class="users">
  <tr>
    <td><b>Last Name</b></td>
    <td><b>First Name</b></td>
    <td><b>Username</b></td>
    <td><b>Email</b></td>
  </tr>
  <%= render @users %>
</table>
<%= will_paginate %>
```
* Create the file app/views/users/_user.html.erb with the following content to show information on each user in the index:
```
<% require 'email_munger' %>
<tr>
  <td><%= user.last_name %></td>
  <td><%= user.first_name %></td>
  <td><%= link_to user.username, user %></td>
  <td><%= link_to raw(EmailMunger.encode(user.email)), user %></td>
</tr>
```
* Create the file app/views/users/_condition_fields.html.erb with the following content to provide the ability to display and remove fields in the search form:
```
<div class="field">
  <%= f.attribute_fields do |a| %>
    <%= a.attribute_select associations: [:category] %>
  <% end %>
  <%= f.predicate_select %>
  <%= f.value_fields do |v| %>
    <%= v.text_field :value %>
  <% end %>
  <%= link_to "remove", '#', class: "remove_fields" %>
</div>
```
* Add the following lines to the end of app/helpers/application_helper.rb (just before the final "end" command):
```
  def link_to_add_fields(name, f, type)
    new_object = f.object.send "build_#{type}"
    id = "new_#{type}"
    fields = f.send("#{type}_fields", new_object, child_index: id) do |builder|
      render(type.to_s + '_fields', f: builder)
    end
    link_to(name, '#', class: 'add_fields',
                       data: { id: id, fields: fields.delete("\n") })
  end
```
* Enter the command "sh testc.sh".  All of the controller tests should now pass.

### Getting Integration Tests To Pass
* Enter the command "test1".  All 5 of the integration tests should now pass.
* Enter the command "test2".  Two of the tests fail, because the expected "User Index" link is not present.
* Add the following line to the beginning of the admin section in app/views/layouts/_header.html.erb:
```
<li><%= link_to "User Index",   users_path %></li>
```
* Enter the command "test2".  Now all 4 of the integration tests should pass.
* Enter the command "test3".  Now all 3 of the integration tests should pass.

### Updating the User Index Appearance
* If you have not already done so, restart the local server, and then refresh the browser.  (If you do not, you'll may get an error message when you attempt to view the user index.)
* Log in to your local app as an admin.
* Go to the URL http://localhost:3000/users.  The user index will appear, but the table would look far better with lines between each entry.
* Add the following content to the end of app/assets/stylesheets/custom.scss to format the table in the users index page:
```
/* Users and admins indices */
.users, .admins {
  td {
    border: 1px solid black;
    padding: 5px;
    text-align: center;
  }
  th {
    border: 1px solid black;
    padding: 5px;
    text-align: center;
  }
}
```
* The admins index page (which you will create later) will use the same table formatting.
* Refresh the user index page.  Now the table looks better.

### Adding and Removing Fields
* The search form appears at the top of the user index page, but the buttons for adding and removing fields do not work yet.
* Add the following content to the end of the app/assets/javascripts/users.coffee file to allow the buttons for adding and removing fields in the search form:
```
jQuery ->
  $('form').on 'click', '.remove_fields', (event) ->
    $(this).closest('.field').remove()
    event.preventDefault()

  $('form').on 'click', '.add_fields', (event) ->
    time = new Date().getTime()
    regexp = new RegExp($(this).data('id'), 'g')
    $(this).before($(this).data('fields').replace(regexp, time))
    event.preventDefault()
```
* Now the buttons for adding and removing fields in the user index search form should work.  Try them out on the browser in your local app to verify this.

### Limiting Searchable Fields
* So far, the search form offers all parameters for filtering search results.  This is confusing and not desirable.  In addition, let's also change the number of entries listed per page from 30 (default value) to 50.
* Add the following lines to app/models/user.rb immediately after the line beginning with "class User":
```
  # Specify the number of entries per page given use of the will_paginate gem
  self.per_page = 50

  # Limit the parameters available for searching the user database
  RANSACKABLE_ATTRIBUTES = %w(email username last_name first_name).freeze
  def self.ransackable_attributes(_auth_object = nil)
    RANSACKABLE_ATTRIBUTES + _ransackers.keys
  end
```
* Refresh your web browser.  Now the only searchable fields should be the email address, username, last name, and first name.

### Rails Best Practices
* Enter the command "sh git_check.sh".  All of the tests should pass, but Rails Best Practices flags app/controllers/users_controller.rb with the message "move model logic into model and config/routes.rb with the message "restrict auto
-generated routes".  Because I was unable to figure out how to make the suggested changes and believe that these are false alarms, the next step is to update config/rails_best_practices.yml
* In the file config/rails_best_practices.yml, make the following changes:
  * Replace the line beginning with "MoveModelLogicIntoModelCheck" with the following line:
  ```
  MoveModelLogicIntoModelCheck: { ignored_files: ['app/controllers/users_controller.rb'] }
  ```
  * Replace the line beginning with "RestrictAutoGeneratedRoutesCheck" with the following line:
  ```
  RestrictAutoGeneratedRoutesCheck: { ignored_files: ['config/routes'] }
  ```
* Enter the command "rails_best_practices".  There should be no remaining issues.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no flagged issues.
* Enter the following commands:
```
git add .
git commit -m "Added user methods"
```

### Wrapping Up
* Enter the command "git push origin 10-01-user_methods".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* The email display on the user profile page will be flagged by Hakiri.  Mark this as a false positive by clicking on Details -> 1 warning -> admin view -> 1 warning -> Mark as False Positive.
* The dynamic render path in the user index page will be flagged by Hakiri.  Mark this issue as a false positive as well.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
