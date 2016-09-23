# Unit 4
## Chapter 5: Code Climate Coverage Test

### New Branch
Enter the command "git checkout -b code_climate_coverage".

### Code Climate Instructions
* On the Code Climate project page, click on the "Set Up Test Coverage" button.
* On the Test Setup page, click on the "Ruby" tab to see the instructions for setting up test coverage.  (The procedure you will use here has some differences from the procedures provided by Code Climate.)
* There are no instructions for getting test coverage data from Circle CI, but the procedure is actually easier than the ones for working with Travis, Solano Labs, and Semaphore.

### Gemfile
* In the Gemfile, replace the SimpleCov section with the following code:
``` 

gem 'codeclimate-test-reporter', group: :test, require: nil
```
Replace the beginning of the test/test_helper.rb file (before the Minitest section). This file should appear as follows:
require 'codeclimate-test-reporter'
CodeClimate::TestReporter.start

def run_simple_cov
  require 'simplecov'
  SimpleCov.start 'rails' do
    add_filter 'app/channels/*'
    add_filter 'app/jobs/*'
  end
end

# save to CircleCI's artifacts directory if we're on CircleCI
if ENV['CIRCLE_ARTIFACTS']
  dir = File.join(ENV['CIRCLE_ARTIFACTS'], 'coverage')
  SimpleCov.coverage_dir(dir)
  run_simple_cov
end

run_simple_cov if ENV['EXEC_SIMPLE_COV'] == 'true'

ENV['RAILS_ENV'] ||= 'test'
require File.expand_path('../../config/environment', __FILE__)
require 'rails/test_help'

# BEGIN: Minitest section
...
Enter the command "sh git_check.sh".
Enter the following commands:
git add .
git commit -m "Added Code Climate gem"
git push origin master
In the Code Coverage instructions, note the value of the CODECLIMATE_REPO_TOKEN variable.
Go back to your project page at the Circle CI web site. Click on "Project Settings", and click on "ENVIRONMENT VARIABLES". Enter the environment variable name and value specified by Code Climate.
