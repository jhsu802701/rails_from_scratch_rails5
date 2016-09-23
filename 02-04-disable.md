# Unit 2
## Chapter 4: Disabling Dangerous Tasks in Production
No new branch is needed in this chapter.

### Configuration
*  Making it easy to destroy data in the development and test environments is desirable.  However, there should be barriers to this in the production environment to prevent disasters.
* Create the file lib/tasks/disable_db_tasks_on_production.rake with the following content:
```
# PURPOSE:
# Prevent data from being altered by rake commands in the production environment
# SOURCE:
# http://www.developingandrails.com/2015/01/disable-dangerous-rake-tasks-in.html

DISABLED_TASKS = ['db:drop', 'db:migrate:reset', 'db:schema:load',
                  'db:seed'].freeze

namespace :db do
  desc 'Disable a task in production environment'
  task :guard_for_production do
    if Rails.env.production?
      if ENV['I_KNOW_THIS_MAY_SCREW_THE_DB'] != '1'
        puts 'This task is disabled in production.'
        puts 'If you really want to run it, '
        puts 'call it again with `I_KNOW_THIS_MAY_SCREW_THE_DB=1`'
        exit
      end
    end
  end
end

DISABLED_TASKS.each do |task|
  Rake::Task[task].enhance ['db:guard_for_production']
end
```
### Executing Dangerous Tasks in the Production Environment
* Create the file temp1.sh with the following contents:
```
#!/bin/bash

echo '****************'
echo 'EXPECTED RESULT:'
echo 'This task is disabled in production.'
echo '************************************'
rails db:drop RAILS_ENV=production
rails db:migrate:reset RAILS_ENV=production
rails db:schema:load RAILS_ENV=production
rails db:seed RAILS_ENV=production

echo ''
echo '****************'
echo 'EXPECTED RESULT:'
echo 'Rails db commands ENABLED'
echo '*************************'

I_KNOW_THIS_MAY_SCREW_THE_DB=1 RAILS_ENV=production rails db:drop
I_KNOW_THIS_MAY_SCREW_THE_DB=1 RAILS_ENV=production rails db:migrate:reset
I_KNOW_THIS_MAY_SCREW_THE_DB=1 RAILS_ENV=production rails db:schema:load
I_KNOW_THIS_MAY_SCREW_THE_DB=1 RAILS_ENV=production rails db:seed

unset RAILS_ENV
unset I_KNOW_THIS_MAY_SCREW_THE_DB
echo ''
echo '****************'
echo 'EXPECTED RESULT:'
echo 'Rails db commands ENABLED'
echo '*************************'
rails db:drop
rails db:migrate:reset
rails db:schema:load
rails db:seed
```
* Remove the temp1.sh script by entering "rm temp1.sh".

### Wrapping Up
* Enter the command "sh git_check.sh".  Only the file lib/tasks/disable_db_tasks_on_production.rake should be listed.
Enter the following commands to push this change to GitHub and Heroku:
```
git add .
git commit -m "Disabled dangerous tasks in production"
git push origin master
sh heroku.sh
```
