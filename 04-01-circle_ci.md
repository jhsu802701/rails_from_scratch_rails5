# Unit 4
## Chapter 1: Circle CI

### Why use Circle CI?
* Like the local development environment, Circle CI uses the SimpleCov gem for providing coverage test results for the Code Climate CI to display.  Other continuous integration systems produce different results and make it more difficult to exclude files and directories from coverage tests.
* It's easier to add the Code Climate repo token value to Circle CI than is the case with other CI systems.  
* Code Climate's instructions for working with Travis CI include entering the repo token value to your source code, which is considered to be a security no-no.

### Setting Up On the Circle CI Web Site
* Log in to the [Circle CI](http://www.circleci.com) web site.  If you haven't already done so, you need to create an account first.
* In the CircleCI dashboard, click on the "Add Projects" icon.  Click on the "Build project" button for your project.

### Circle CI Test Results
After the test has finished and given you results, click on "Project Settings".  Then click on "Status Badges" to see the status badge for the build state.
