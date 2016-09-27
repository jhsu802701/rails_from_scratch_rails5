# Unit 3
## Chapter 1: Minitest Reporters
In this chapter, you will set up Minitest Reporters.  It provides features like red/green displays and a list of the most time-consuming tests within your test suite.

### New Branch
Enter the command "git checkout -b 03-01-minitest_reporters".

### Gemfile
*  Add the following line to the end of the Gemfile:
```
# Minitest
gem 'minitest', require: :false, group: :testing
gem 'minitest-reporters', require: :false, group: :testing # Adds special features to tests
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Installed minitest-reporters"
```
### Configuration
* Add the file test/rake_rerun_reporter.rb and give it the following contents:
```
# From https://gist.github.com/foton/141b9f73caccf13ccfcc

require 'minitest/reporters'

module Minitest
  module Reporters
    class RakeRerunReporter < Minitest::Reporters::DefaultReporter

      def initialize(options = {})
        @rerun_user_prefix=options.fetch(:rerun_prefix, '')
        super
      end

      def report
        super
      
        puts
      
        unless @fast_fail
          #print rerun commands
          failed_or_error_tests=(tests.select {|t| t.failure && !t.skipped? })

          unless failed_or_error_tests.empty?
            puts red("You can rerun failed/error test by commands (you can add rerun prefix with 'rerun_prefix' option):")

            failed_or_error_tests.each do |test|
              print_rerun_command(test)
            end
          end
        end
        
        #summary for all suite again
        puts
        print colored_for(suite_result, result_line)
        puts
        
      end  

      private

        def print_rerun_command(test)
          message = rerun_message_for(test)
          unless message.nil? || message.strip == ''
            puts
            puts colored_for(result(test), message)
          end
        end

        def rerun_message_for(test)
          file_path=location(test.failure).gsub(/(\:\d*)\z/,"")
          msg="#{@rerun_user_prefix} rake test TEST=#{file_path} TESTOPTS=\"--name=#{test.name} -v\""
          if test.skipped?
            "Skipped: \n#{msg}"
          elsif test.error?
            "Error:\n#{msg}"
          else
            "Failure:\n#{msg}"
          end
        end
        
        def location(exception)
          last_before_assertion = ''

          exception.backtrace.reverse_each do |ss|
            break if ss =~ /in .(assert|refute|flunk|pass|fail|raise|must|wont)/
            last_before_assertion = ss
            break if ss=~ /_test.rb\:/
          end

          last_before_assertion.sub(/:in .*$/, '')
        end  

    end
  end
end
```
* In the test/test_helper.rb file, insert the code for configuring Minitest Reporters between the line "require 'rails/test_help'" and the line "class ActiveSupport::TestCase".  Your code should look like this:
```
...
require 'rails/test_help'

# BEGIN: use minitest-reporters
# AwesomeReporter configuration from
# http://chriskottom.com/blog/2014/06/dress-up-your-minitest-output/
# RakeRerunReporter from
# https://gist.github.com/foton/141b9f73caccf13ccfcc

require 'minitest/reporters'
Minitest::Reporters.use!

module Minitest
  module Reporters
    class AwesomeReporter < DefaultReporter
      GREEN = '1;32'.freeze
      RED = '1;31'.freeze

      def color_up(string, color)
        color? ? "\e\[#{color}m#{string}#{ANSI::Code::ENDCODE}" : string
      end

      def red(string)
        color_up(string, RED)
      end

      def green(string)
        color_up(string, GREEN)
      end
    end
  end
end

reporter_options = { color: true, slow_count: 5, verbose: false, rerun_prefix: 'rm -f log/test.log && bundle exec' }
Minitest::Reporters.use! [Minitest::Reporters::AwesomeReporter.new(reporter_options),
                          Minitest::Reporters::HtmlReporter.new,
                          Minitest::Reporters::RakeRerunReporter.new(reporter_options)]
# END: use minitest-reporters

class ActiveSupport::TestCase
...
```
* Replace the contents of the .rubocop.yml with the following code:
```
AllCops:
  Exclude:
    - db/schema.rb
    - tmp/vulnerabilities/lib/*
    - tmp/vulnerabilities/spec/*
    - tmp/vulnerabilities/Rakefile

Cop/MethodLength:
  Exclude:
    - test/rake_rerun_reporter.rb

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
    - test/rake_rerun_reporter.rb
    - test/test_helper.rb

Style/ClassAndModuleChildren:
  Exclude:
    - test/test_helper.rb
```
* Add the following line to the end of .gitignore:
```
test/html_reports/
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Updated test/test_helper.rb for minitest-reporters"
```

### First Test
* Enter the command "rails generate integration_test test1".  This creates the file test/integration/test1_test.rb.
* Give test/integration/test1_test.rb the following content:
```
require 'test_helper'

class Test1Test < ActionDispatch::IntegrationTest
  test 'home page has expected title and heading' do
    get '/'
    assert_select 'title', 'Home'
    assert_select 'h1', 'Home'
  end

  test 'home page has expected content' do
    get '/'
    assert_match 'Welcome to public/index.html!', response.body
  end
end
```
* Enter the command "rails test".  You'll see that the test for the title and heading fail.
* Replace the contents of the public/index.html file with the following:
```
<html>
    <head>
        <title>Home</title>
    </head>
    <body>
	<h1>Home</h1>	
    Welcome to public/index.html!
    </body>
</html>
```
* Enter the command "rails test".  Now all of the tests pass, and the display output contains green instead of red.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Successfully completes first test"
git push origin 03-01-minitest_reporters
```
### Reset
* Follow the instructions in the README.md file for resetting the Docker container.  After you have downloaded the source code with the "git clone" command, enter "git checkout 03-01-minitest_reporters" to switch to this alternate branch.
* If all goes as expected, you are ready to submit a pull request and merge this branch into the master branch.

### Wrapping Up
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
