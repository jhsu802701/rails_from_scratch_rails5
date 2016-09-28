# Unit 5
## Chapter 2: Creating Static Pages

### New Branch
Enter the command "git checkout -b 05-02-static_pages".

### Gemfile
* Add the following line to the end of the Gemfile:
```
gem 'email_munger' # Encodes email address to prevent harvesting by bots
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added email_munger gem"
```

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
* Delete the file app/helpers/static_pages_helper.rb, because you won't be using this helper.
* Enter the command "sh testcl.sh".  The only issue remaining is the unused method full_title in the application helper.  Again, you will address this later.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added static pages controller"
```

### Static Pages: Views
* Remove the following files:
```
public/index.html
public/about.html
test/integration/test1_test.rb
test/integration/test2_test.rb
test/integration/test3_test.rb
```
* Enter the command "rails test" to make sure you have removed the initial content and initial integration tests.
* In your browser window connected to the local server, you'll find your static pages at http://localhost:3000/, http://localhost:3000/about, and http://localhost:3000/contact .  (NOTE: If you are using Docker Machine, replace localhost with the appropriate numerical IP address.  If your Docker container outputs to a host port other than 3000, replace the "3000" with the appropriate port number.)
* Enter the command "rails generate integration_test static_pages".
* Replace the content of the file test/integration_test/static_pages_test.rb with the following code:
```
require 'test_helper'

class StaticPagesTest < ActionDispatch::IntegrationTest
  test 'home page has expected content' do    
  end

  test 'about page has expected content' do    
  end

  test 'contact page has expected content' do    
  end

  test 'home page provides access to the about and contact pages' do
  end

  test 'about page provides access to the home and contact pages' do
  end

  test 'contact page provides access to the home and about pages' do
  end
end
```
* Replace the content o
* Enter the following commands:
```
git add .
git commit -m "Added static pages"
```

### Wrapping Up
