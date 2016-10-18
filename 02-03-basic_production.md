# Unit 2
## Chapter 3: Basic Production Environment Configuration

### SSL in Production

*  Edit the config/environments/production.rb file and uncomment the line containing "config.force_ssl = true".
This means that SSL is automatically enabled.
* Enter the command "sh git_check.sh".
*  Enter the following commands to push this change to GitHub and Heroku:
```
git add .
git commit -m "Use SSL in production"
git push origin master
```
* Enter the command "sh heroku.sh".
* When you use your browser to view your project, note the presence of "https://" and the lock icon in the address bar.
This signals that SSL is working.

### Production Web Server

* Replace the contents of the config/puma.rb file with the following:

```
workers Integer(ENV['WEB_CONCURRENCY'] || 2)
threads_count = Integer(ENV['RAILS_MAX_THREADS'] || 5)
threads threads_count, threads_count

preload_app!

rackup      DefaultRackup
port        ENV['PORT']     || 3000
environment ENV['RACK_ENV'] || 'development'

on_worker_boot do
  # Worker specific setup for Rails 4.1+
  # See: https://devcenter.heroku.com/articles/
  # deploying-rails-applications-with-the-puma-web-server#on-worker-boot
  ActiveRecord::Base.establish_connection
end
```
* This replaces the default WEBrick server used in Heroku with Puma.  Puma can handle FAR more traffic than WEBrick.
* In the project's root directory, create the file Procfile and give it the following contents:
```
web: bundle exec puma -C config/puma.rb
```
* Enter the command "sh git_check.sh".
*  Enter the following commands to push this change to GitHub and Heroku:
```
git add .
git commit -m "Use Puma web server in production"
git push origin master
```
* Enter the command "sh heroku.sh".
