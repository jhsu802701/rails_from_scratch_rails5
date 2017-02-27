# Unit 10
## Chapter 3: User Deletion

In this chapter, you will give the power to delete users to admins and ONLY admins.  (Remember that users can delete themselves through the registration edit channel.)  

### New Branch
Enter the command "git checkout -b 10-03-user_delete".

### Controller Test
* In the file test/controllers/users_controller_test.rb, add the following lines to the DELETE section:
```
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
```
* In the file test/controllers/users_controller_test.rb, add the line "# rubocop:disable Metrics/ClassLength" to the very beginning of the file and the line "# rubocop:disable Metrics/ClassLength" to the very end of the file.
* Enter the command "sh testc.sh".  Five controller tests fail because the destroy action is missing from the user controller.
* In the file app/controllers/users_controller.rb, add the following lines to the before_action section:
```
  before_action :may_destroy_user, only: [:destroy]
```
* In the file app/controllers/users_controller.rb, add the following lines to the action section:
```
  def destroy
    User.find(params[:id]).destroy
    flash[:success] = 'User deleted'
    redirect_to(users_path)
  end
```
* In the file app/controllers/users_controller.rb, add the following lines to the private section:
```
  def may_destroy_user
    return redirect_to(root_path) unless admin_signed_in?
  end
  helper_method :may_destroy_user
```
* Enter the command "sh testc.sh".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added destroy to the user controller"
```

### Integration Test
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
* Enter the command "sh build_fast.sh".  Two tests will fail.
* Enter the command "alias test1='(command for rerunning test minus the TESTOPTS portion)'".
* Enter the command "test1".  Two tests will fail because the delete button is missing from the user profile page.
* Add the delete button to the bottom of the user profile .  Edit the file app/views/users/show.html.erb.  The file should look like this:
```
    . . . .
    <br>
    <% ###################### %>
    <% # BEGIN: delete button %>
    <% ###################### %>
    <% if admin_signed_in? %>
      <%= link_to "Delete #{@user.first_name} #{@user.last_name} (#{@user.username})", @user,
        class: "btn btn-primary", method: :delete,
        data: { confirm: "Are you sure you wish to delete #{@user.first_name} #{@user.last_name}?" } %>
    <% end %>
    <% #################### %>
    <% # END: delete button %>
    <% #################### %>
  </section>
</div>
```

### Wrapping Up
* Enter the command "git push origin 10-03-user_delete".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
