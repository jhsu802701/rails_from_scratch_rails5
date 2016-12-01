# Unit 10
## Chapter 3: Indexing Users

### New Branch
Enter the command "git checkout -b 10-03-users_index".

### User Index Test
* In the test/controllers/users_controller_test.rb file, add the following lines just before the final "end" line:
```
  test 'should redirect index page when not logged in' do
    get :index
    assert_redirected_to root_path
  end

  test 'should redirect index page when logged in as a user' do
    get :index
    assert_redirected_to root_path
  end

  test 'should not redirect index page when logged in as a regular admin' do
    get :index
    assert :success
  end

  test 'should not redirect index page when logged in as a super admin' do
    get :index
    assert :success
end
```
* Enter the command "sh testc.sh".  All 4 of these new user controller tests will fail because the routing is missing.

### Routing
* In the user section of config/routes.rb, replace the line beginning with "resources :users" with the following:
```
  resources :users, only: [:show]
```
* Enter the command "sh testc.sh". All 4 user controller tests will still fail because the show action is not found.

### User Controller


### Wrapping Up
* Enter the command "git push origin 10-03-users_index".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
