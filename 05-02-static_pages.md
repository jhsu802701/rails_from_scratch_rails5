# Unit 5
## Chapter 2: Creating Static Pages

### New Branch
Enter the command "git checkout -b 05-02-static_pages".

### Static Pages: Controller
* Enter the commmand "rails generate controller StaticPages home about contact".  The config/routes.rb is automatically edited.  (Routes are added.)
* Replace the content of test/controllers/static_pages_controller_test.rb with the following code:
```
require 'test_helper'

class StaticPagesControllerTest < ActionDispatch::IntegrationTest
  test 'should get home' do
    get root_path
    assert_response :success
  end

  test 'should get about' do
    get about_path
    assert_response :success
  end

  test 'should get contact' do
    get contact_path
    assert_response :success
  end
end
```
* Enter the command "sh testc.sh".  You'll see that all of these static page controller tests fail.
* Replace the content of config/routes.rb with the following code:
```
# For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
Rails.application.routes.draw do
  root 'static_pages#home'
  get '/help', to: 'static_pages#help'
  get '/about', to: 'static_pages#about'
  get '/contact', to: 'static_pages#contact'
end
```
* Enter the command "sh testc.sh".  You'll see that the controller tests now pass.
* Enter the command "sh testcl.sh".  You'll see that RuboCop and Rails Best Practices raise issues.
* Replace the content of app/controllers/static_pages_controller.rb with the following:
```
#
class StaticPagesController < ApplicationController
  def home
  end

  def about
  end

  def contact
  end
end
```

### Static Pages: Views

### Wrapping Up
