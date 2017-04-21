# Unit 11
## Chapter 3: Admin Deletion

### New Branch
Enter the command "git checkout -b 11-03-admin_delete".

### Controller Test
* In test/controllers/admins_controller_test.rb, add the following content to the delete section:
```
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

  # NOTE: Admin can delete self through edit registration form.
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
```

### Controller
* In app/controllers/admins_controller.rb, add the following line to the before_action section:
```
  before_action :may_destroy_admin, only: [:destroy]
```
* In app/controllers/admins_controller.rb, add the following lines to the action section:
```
  def destroy
    Admin.find(params[:id]).destroy
    flash[:success] = 'Admin deleted'
    redirect_to(admins_path)
  end
```
* In app/controllers/admins_controller.rb, add the following lines to the private section:
```
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
```

### Integration Tests
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
    login_as(@a5, scope: :admin)
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
* Enter the command "sh build_fast.sh".
* Enter the command "alias test1='(command for repeating the failed tests minus the TESTOPTS portion)'".
* Enter the command "test3".
* In the file app/views/admins/show.html.erb, add the following lines after the email display:
```
    <br>
    <% if @admin.super != true && current_admin.super == true %>
      <%= link_to "Delete", @admin, method: :delete,
                            data: { confirm: "Are you sure you wish to delete this admin?" },
                            class: "btn btn-lg btn-primary"
      %>
```

### Wrapping Up
* Enter the command "git push origin 11-03-admin_delete".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
