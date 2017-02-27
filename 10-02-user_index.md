# Unit 10
## Chapter 2: User Index

In this chapter, you will provide the index method for users.  The index of users should be available ONLY to the admins and not to the users or the general public.

### New Branch
Enter the command "git checkout -b 10-02-user_index_controller".

### Controller Test
* Add the following text to the index section of the file test/controllers/users_controller_test.rb:
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
* Enter the command "sh testc.sh".

### Routing
* In the config/routes.rb file, replace the line beginning with "resources :users" with the following:
```
resources :users, only: [:show, :index]
```
* Enter the command "sh testc.sh".

### User Controller
* In the before_action section of the file app/controllers/users_controller.rb, add the following line:
```
  before_action :may_index_user, only: [:index]
```
* In the before_action section of the file app/controllers/users_controller.rb,


### Wrapping Up
* Enter the command "git push origin 10-02-user_index_controller".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* The dynamic render path in the user index page will be flagged by Hakiri. Mark this issue as a false positive.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
