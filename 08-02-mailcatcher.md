# Unit 8
## Chapter 2: MailCatcher

The MailCatcher tool provides an SMTP (Simple Mail Transfer Protocol) server so that you can view emails sent by your Rails app to the simulated admins and users in your development environment.  This allows you to verify that the email functionality works WITHOUT spamming the real users in your production environment.

### New Branch
Enter the command "git checkout -b 08-02-mailcatcher".

### Gemfile
Although MailCatcher is a gem, you should NOT add it to your Gemfile, because it will likely conflict with other gems sooner or later.

### Configuration
* Edit the file environments/development.rb.  You will add code to the end of this file, which will look something like this:
```
...
  # From http://mailcatcher.me/
  config.action_mailer.delivery_method = :smtp
  config.action_mailer.smtp_settings = { address: 'localhost', port: 1025 }
end
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Configured environments/development.rb for MailCatcher"
```
### mailcatcher.sh
* Add the file mailcatcher.sh with the following content:
```
#!/bin/bash

gem install mailcatcher
mailcatcher --ip 0.0.0.0
echo 'View mail at http://localhost:<port number>/'
echo 'If you are developing this app in the host environment, the port number is 1080.'
echo 'If you are using Docker or Vagrant, the port number may be something else.'
echo 'If you are using Docker Machine, replace "localhost" with the appropriate numerical IP address (probably 192.168.99.100).'
# Send mail through smtp://localhost:1025
```
* Enter the command "sh mailcatcher.sh".
* In the browser window in the host environment, go to the appropriate URL.  You should see the MailCatcher interface, but there won't be any messages.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added mailcatcher.sh"
git push origin 08-02-mailcatcher
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
