# Unit 10
## Chapter 2: Admin Index

In this chapter, you will provide the index method for admins.  The index of admins should be available ONLY to the admins and not to the users or the general public.

### New Branch
Enter the command "git checkout -b 11-02-admin_index".

### Controller Tests
* Add the following text to the index section of test/controllers/admins_controller_test.rb:
```
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
```
* Enter the command "sh testc.sh".  All 4 tests fail because of a missing route.
* Edit the config/routes.rb file and replace the line beginning with "resources :admins" with the following:
```
  resources :admins, only: [:show, :index]
```
* Enter the command "sh testc.sh".  All 4 tests fail because of a missing action in the admin controller.
* In the before_action section of the file app/controllers/admins_controller.rb, add the following line:
```
before_action :may_index_admin, only: [:index]
```
* In the action section of app/controllers/admins_controller.rb, add the following lines:
```
  def index
    @admins = Admin.paginate(page: params[:page])
  end
```
* In the private section of the file app/controllers/admins_controller.rb, add the following lines:
```
  def may_index_admin
    may_show_admin
  end
  helper_method :may_index_admin
```
* Enter the command "sh testc.sh".  All 4 tests fail because of a missing template.
* Enter the command "touch app/views/admins/index.html.erb" to create the admin index page.  (You'll fill it in later.)
* Enter the command "sh testc.sh".  All controller tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no violations.
* Enter the following commands:
```
git add .
git commit -m "Added admin index capability in the controller"
```

### Integration Tests
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
  def check_index_enabled
    visit admins_path
    assert page.has_css?('title', text: full_title('Admin Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Admin Index')
    assert page.has_text?('Elle')
    assert page.has_text?('Woods')
    assert page.has_text?('Vivian')
    assert page.has_text?('Kensington')
    assert page.has_text?('Emmett')
    assert page.has_text?('Richmond')
    assert page.has_text?('Paulette')
    assert page.has_text?('Bonafonte')
    assert page.has_text?('Professor')
    assert page.has_text?('Callahan')
    assert page.has_text?('Warner')
    assert page.has_text?('Huntington')
    assert page.has_text?('elle_woods@example.com')
    assert page.has_text?('vivian_kensingston@example.com')
    assert page.has_text?('emmett_richmond@example.com')
    assert page.has_text?('paulette_bonafonte@example.com')
    assert page.has_text?('professor_callahan@example.com')
    assert page.has_text?('warner_huntington@example.com')

    # Verify that index page provides access to profile pages
    assert page.has_link?('ewoods', href: admin_path(@a1))
    assert page.has_link?('elle_woods@example.com', href: admin_path(@a1))
    assert page.has_link?('vkensington', href: admin_path(@a2))
    assert page.has_link?('vivian_kensingston@example.com', href: admin_path(@a2))
    assert page.has_link?('erichmond', href: admin_path(@a3))
    assert page.has_link?('emmett_richmond@example.com', href: admin_path(@a3))
    assert page.has_link?('pbonafonte', href: admin_path(@a4))
    assert page.has_link?('paulette_bonafonte@example.com', href: admin_path(@a4))
    assert page.has_link?('pcallahan', href: admin_path(@a5))
    assert page.has_link?('professor_callahan@example.com', href: admin_path(@a5))
    assert page.has_link?('whuntington', href: admin_path(@a6))
    assert page.has_link?('warner_huntington@example.com', href: admin_path(@a6))

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

  test 'admins index page is accessible to regular admins' do
    login_as(@a4, scope: :admin)
    check_index_enabled
  end

  test 'admins index page is accessible to super admins' do
    login_as(@a1, scope: :admin)
    check_index_enabled
  end
end
```
* Enter the command "sh build_fast.sh".  Two tests fail due to missing content on the admin index page.
* Enter the command "alias test1='(command for repeating the tests that failed minus the TESTOPTS portion)'".
* Enter the command "test1".  Two tests should fail.
* Give the file app/views/admins/index.html.erb the following content:
```
<% provide(:title, 'Admin Index') %>

<h1>Admin Index</h1>

<table class="admins">
  <tr>
    <td><b>Last Name</b></td>
    <td><b>First Name</b></td>
    <td><b>Super?</b></td>
    <td><b>Username</b></td>
    <td><b>Email</b></td>
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
  <td><%= link_to raw(EmailMunger.encode(admin.email)), admin %></td>
</tr>
```
* Enter the command "test1".  Two tests should fail due to a missing link to the admin index from the home page.
* In the admin section in app/views/layouts/_header.html.erb, add the following line just after the one containing "User Index":
```
          <li><%= link_to "Admin Index", admins_path %></li>
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Completed the index method for admins"
```

### Wrapping Up
* Enter the command "git push origin 11-02-admin_index".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
