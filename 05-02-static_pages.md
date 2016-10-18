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
* Enter the command "rake routes".  This lists the routes available.  Given that the routes "root", "about", and "contact" (the paths listed in the static controller tests), it should be no surprise that the tests failed.
* Replace the content of config/routes.rb with the following code:
```
# For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
Rails.application.routes.draw do
  # BEGIN: static pages
  root 'static_pages#home'
  get '/about', to: 'static_pages#about'
  get '/contact', to: 'static_pages#contact'
  # END: static pages
end
```
* Enter the command "rake routes".  Now you'll see the routes "root", "about", and "contact".
* Enter the command "sh testc.sh".  You'll see that the controller tests now pass.
* Enter the command "sh testcl.sh".  You'll see that RuboCop and Rails Best Practices raise issues.
* Enter the command "rm app/helpers/static_pages_helper.rb".
* In the files app/controllers/static_pages_controller.rb, add the following line immediately before the line beginning with "class":
```
#
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
* Enter the following commands:
```
git rm public/index.html
git rm public/about.html
git rm test/integration/test1_test.rb
git rm test/integration/test2_test.rb
git rm test/integration/test3_test.rb
```
* Enter the command "rails test" to make sure you have removed the initial content and initial integration tests.
* In your browser window connected to the local server, you'll find your static pages at http://localhost:3000/, http://localhost:3000/about, and http://localhost:3000/contact .  (NOTE: If you are using Docker Machine, replace localhost with the appropriate numerical IP address.  If your Docker container outputs to a host port other than 3000, replace the "3000" with the appropriate port number.)
* Enter the command "rails generate integration_test static_pages".
* Replace the content of the file test/integration_test/static_pages_test.rb with the following code:
```
require 'test_helper'
include ApplicationHelper

class StaticPagesTest < ActionDispatch::IntegrationTest
  test 'home page has expected content' do
    visit root_path
    assert page.has_css?('title', text: full_title(''), visible: false)
    assert page.has_css?('h1', text: 'Home')
    assert page.has_text?('Welcome to Generic App Template')
    assert page.has_css?('small', text: 'Generic App Template by Somebody')
  end

  test 'about page has expected content' do
    visit about_path
    assert page.has_css?('title', text: full_title('About'), visible: false)
    assert page.has_css?('h1', text: 'About')
    assert page.has_text?('Describe your site here.')
    assert page.has_css?('small', text: 'Generic App Template by Somebody')
  end

  test 'contact page has expected content' do
    visit contact_path
    assert page.has_css?('title', text: full_title('Contact'), visible: false)
    assert page.has_css?('h1', text: 'Contact')
    assert page.has_text?('somebody@rubyonracetracks.com')
    assert page.has_css?('small', text: 'Generic App Template by Somebody')
  end

  test 'home page provides access to the about page' do
    visit root_path
    click_on 'About'
    assert page.has_css?('title', text: full_title('About'), visible: false)
    assert page.has_css?('h1', text: 'About')
  end

  test 'home page provides access to the contact page' do
    visit root_path
    click_on 'Contact'
    assert page.has_css?('title', text: full_title('Contact'), visible: false)
    assert page.has_css?('h1', text: 'Contact')
  end

  test 'about page provides access to the home page' do
    visit about_path
    click_on 'Home'
    assert page.has_css?('title', text: full_title(''), visible: false)
    assert page.has_css?('h1', text: 'Home')
  end

  test 'about page provides access to the contact page' do
    visit about_path
    click_on 'Contact'
    assert page.has_css?('title', text: full_title('Contact'), visible: false)
    assert page.has_css?('h1', text: 'Contact')
  end

  test 'contact page provides access to the home page' do
    visit contact_path
    click_on 'Home'
    assert page.has_css?('title', text: full_title(''), visible: false)
    assert page.has_css?('h1', text: 'Home')
  end

  test 'contact page provides access to the about page' do
    visit contact_path
    click_on 'About'
    assert page.has_css?('title', text: full_title('About'), visible: false)
    assert page.has_css?('h1', text: 'About')
  end
end
```
* Replace the content of the file app/views/layouts/application.html.erb with the following:
```
<!DOCTYPE html>
<html>
  <head>
    <title><%= full_title(yield(:title)) %></title>
    <%= csrf_meta_tags %>
    <%= stylesheet_link_tag    'application', media: 'all',
                                              'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
    <%= render 'layouts/shim' %>
  </head>
  <body>
    <%= render 'layouts/header' %>
    <div class="container">
      <% flash.each do |message_type, message| %>
        <div class="alert alert-<%= message_type %>"><%= message %></div>
      <% end %>
      <%= yield %>
      <%= render 'layouts/footer' %>
      <%= debug(params) if Rails.env.development? %>
    </div>
  </body>
</html>
```
* Add the file app/views/layouts/_header.html.erb with the following content:
```
<header class="navbar navbar-fixed-top navbar-inverse">
  <div class="container">
    <%= link_to "Generic App Template", root_path, id: "logo" %>
    <nav>
      <ul class="nav navbar-nav navbar-right">
        <li><%= link_to "Home", root_path %></li>
        <li><%= link_to "About", about_path %></li>
        <li><%= link_to "Contact", contact_path %></li>
      </ul>
    </nav>
  </div>
</header>
```
* Add the file app/views/layouts/_footer.html.erb with the following content:
```
<footer class="footer">
  <small>
    Generic App Template by Somebody
  </small>
</footer>
```
* Add the file app/views/layouts/_shim.html.erb with the following content:
```
<!--[if lt IE 9]>
  <script src="//cdnjs.cloudflare.com/ajax/libs/html5shiv/r29/html5.min.js">
  </script>
<![endif]-->
```
* Download a [Ruby on Rails logo] (https://bytebucket.org/railstutorial/sample_app_4th_ed/raw/c9d26c71bc901a9f2e2bb59f5bafc0fc82be7ffb/app/assets/images/rails.png) in png format and save it as app/assets/images/rails.png.
* Replace the content of app/views/static_pages/home.html.erb with the following content:
```
<% provide(:title, '') %>

<div class="center jumbotron">
  <h1>Home</h1>
  Welcome to Generic App Template!
  <br><br>
  <%= link_to image_tag("rails.png", alt: "Rails logo"),
              'http://rubyonrails.org/' %>
</div>
```
* Replace the content of app/views/static_pages/about.html.erb with the following content:
```
<% provide(:title, "About") %>

<h1>About</h1>
Describe your site here.
```
* Replace the content of app/views/static_pages/contact.html.erb with the following content:
```
<% provide(:title, 'Contact') %>

<h1>Contact</h1>
Email address: <%= raw(EmailMunger.encode('somebody@rubyonracetracks.com')) %>
```
* Stop the local web server, and then restart it.  (If you don't, the contact page might not work properly.)
* When you view the contact page in the web browser, the email address appears normal.  However, when you view the page source from the browser, you see that the characters in the email address have been encoded.  This allows human eyes to see the actual email address, but most bots attempting to harvest email addresses will see noise and thus miss this email address.
* Enter the command "rails test".  All tests should pass.
* Enter the command "sh git_check.sh".  RuboCop and Rails Best Practices show no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added static pages"
git push origin 05-02-static_pages
```

### Wrapping Up
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
* The new static pages should now appear on the Heroku site.
