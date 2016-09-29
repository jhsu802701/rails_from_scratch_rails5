# Unit 6
## Chapter 1: Creating Admin and User Models

In this chapter, you will create admin and user models with the Devise gem.  While Rails Tutorial shows how to create an authentication system, the Devise gem has much more credibility.  The purpose of the roll-your-own authentication system in the Rails Tutorial Sample App is to illustrate how authentication works, not to provide the best authentication system.

### New Branch
Start a new branch by entering the command "git checkout -b 06-01-create_models".

### Gemfile
* Add the following line to the end of your Gemfile:
```
gem 'devise' # Provides admin/user authentication
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Installed Devise gem"
```

### Setting Up Devise
* Enter the command "rails generate devise:install".
* Edit the file config/environments/development.rb so that it looks like this:

```
(original content here)
...

  # From https://github.com/plataformatec/devise
  config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
end
```

### Wrapping Up
