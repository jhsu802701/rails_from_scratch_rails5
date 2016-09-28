# Unit 5
## Chapter 1: Title Helper
In this chapter, you will create a title helper.  This will be the basis of the title in each page on your site.

### New Branch
Enter the command "git checkout -b 05-01-title_helper".

### Testing and Creating the Title Helper
* Create the file test/helpers/application_helper_test.rb with the following content:
```
require 'test_helper'

class ApplicationHelperTest < ActionView::TestCase
  test 'full title helper' do
    assert_equal full_title, 'Generic App Template'
    assert_equal full_title('About'), 'About | Generic App Template'
    assert_equal full_title('Contact'), 'Contact | Generic App Template'
    assert_equal full_title('Michael Hartl'), 'Michael Hartl | Generic App Template'
  end
end
```
* Enter the command "sh testh.sh".  Your helper test should fail.
* Edit the file app/helpers/application_helper.rb and give it the following content:
```
#
module ApplicationHelper
  # Returns the full title on a per-page basis
  def full_title(page_title = '')
    base_title = 'Generic App Template'
    if page_title.empty?
      base_title
    else
      page_title + ' | ' + base_title
    end
  end
end
```
* Enter the command "sh testh.sh".  Your helper test should now pass.
* Enter the command "sh testhl.sh".  Rails Best Practices will warn you about an unused method (full_title within ApplicationHelper), but don't worry about this now, because you will use it on every web page on your site.  RuboCop will flag test/helpers/application_helper_test.rb for having a line more than 80 characters long. 
* Make the test/helpers/application_helper_test.rb exempt from the line length cop by replace the contents of the .rubocop.yml file with the following:
```
AllCops:
  Exclude:
    - db/schema.rb
    - tmp/vulnerabilities/lib/*
    - tmp/vulnerabilities/spec/*
    - tmp/vulnerabilities/Rakefile

Metrics/AbcSize:
  Exclude:
    - test/rake_rerun_reporter.rb

Metrics/LineLength:
  Exclude:
    - bin/spring
    - config/application.rb
    - config/environments/development.rb
    - config/environments/production.rb
    - config/initializers/assets.rb
    - config/initializers/backtrace_silencers.rb
    - config/initializers/new_framework_defaults.rb
    - config/initializers/session_store.rb
    - config/initializers/wrap_parameters.rb
    - config/environments/production.rb
    - config/environments/test.rb
    - config/puma.rb
    - db/seeds.rb
    - Gemfile
    - Rakefile
    - test/helpers/application_helper_test.rb
    - test/rake_rerun_reporter.rb
    - test/test_helper.rb

Metrics/MethodLength:
  Exclude:
    - test/rake_rerun_reporter.rb

Style/ClassAndModuleChildren:
  Exclude:
    - test/test_helper.rb
```
* Enter the command "sh testhl.sh".  The RuboCop issue has now been resolved.
* Enter the command "sh git_check.sh".  All tests pass, and RuboCop finds no offenses.  Note that SimpleCov shows improved test coverage now that app/helpers/application_helper.rb is covered.  (Again, you will address the unused method issue flagged by Rails Best Practices after this chapter.)
* Enter the following commands:
```
git add .
git commit -m "Added title helper"
git push origin 05-01-title_helper
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
