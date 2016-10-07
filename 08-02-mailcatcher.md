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
  config.action_mailer.smtp_settings = { :address => "localhost", :port => 1025 }
end
```

### Wrapping Up
