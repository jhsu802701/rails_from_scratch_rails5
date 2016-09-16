# Unit 2
## Chapter 3: Disabling Dangerous Tasks in Production

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
      else
        # NOTE: If you are not using Heroku, you need to modify this section.
        require 'heroku'
        puts 'Making a backup of the database, just in case...'
        puts `heroku pgbackups:capture`
      end
    end
  end
end

DISABLED_TASKS.each do |task|
  Rake::Task[task].enhance ['db:guard_for_production']
end
```
