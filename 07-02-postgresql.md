# Unit 7
## Chapter 2: Configuring PostgreSQL

In this chapter, you will provide scripts for converting your app from the SQLite database to the PostgreSQL database.  I find the use of scripts for configuring PostgreSQL to be essential.  Every time I tried to configure PostgreSQL manually, I frequently screwed up some of the details and had to spend lots of time looking up commands and troubleshooting.

### New Branch
Enter the command "git checkout -b 07-02-postgresql".

### Gemfile
* Add the following lines to the end of the Gemfile:
```
# BEGIN: gems used for setting up PostgreSQL in the development environment
# You do not need these gems if you use SQLite in the development environment.
# NOTE: Attempts to use "gem install" in the PostgreSQL setup scripts did not pan out.
gem 'figaro'
gem 'line_containing'
gem 'remove_double_blank'
gem 'string_in_file'
# END: gems used for setting up PostgreSQL in the development environment
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Added gems for setting up PostgreSQL in development"
```

### config/database-pg.yml
* Create the file config/database-pg.yml with the following content:
```
# POSTGRESQL DATABASE PARAMETERS:
# Database name (development)
# Database name (testing)
# Database name (production)
# Username 
# Password
# NOTE: All three databases use the same username and password.

default: &default
  adapter: postgresql
  pool: 5
  timeout: 5000
  username: <%= ENV['VAR_STORE_USERNAME'] %>
  password: <%= ENV['VAR_STORE_PASSWORD'] %>

development:
  <<: *default
  database: DB_NAME_DEV

test:
  <<: *default
  database: DB_NAME_TEST

production:
  <<: *default
  database: DB_NAME_PRO
```

### Setup Scripts
* Create the file pg_setup.sh with the following content:
```
#!/bin/bash

# Get time as a UNIX timestamp (seconds elapsed since Jan 1, 1970 0:00 UTC)
n="$(date +%s)"

# Output:
# First argument if it is not blank
# Second argument if first argument is blank
anti_blank () {
  if [ -z "$1" ]; then
    echo $2
  else
    echo $1
  fi
}

#######################################################################################
# BEGIN: setting PostgreSQL database name parameters (db_root, db_dev, db_test, db_pro)
#######################################################################################

dir_root=${PWD##*/} # This directory
db_root_def="db_generic_${n}" # Default root name of database
echo '*******************************'
echo 'SELECTING POSTGRESQL PARAMETERS'
echo
echo 'NOTE: Use letters, numbers, and underscores in parameter names.'
echo 'Hyphens are prohibited.'
echo
echo "Default database root name: ${db_root_def}"
echo "Default database name (development): ${db_root_def}_dev"
echo "Default database name (testing): ${db_root_def}_test"
echo "Default database name (production): ${db_root_def}_pro"
echo 'Enter your desired database root name:'
read db_root_sel
db_root=$(anti_blank $db_root_sel $db_root_def)

db_dev="${db_root}_dev"
db_test="${db_root}_test"
db_pro="${db_root}_pro"

#################################################################################
# FINISHED: setting PostgreSQL database name parameters (db_dev, db_test, db_pro)
#################################################################################

####################################################################################
# BEGIN: setting names of environment variables (env_var_username, env_var_password)
####################################################################################

env_var_root_def="var_${db_root}"
echo
echo 'Default environmental variable names'
echo "Username: ${env_var_root_def}_username"
echo "Password: ${env_var_root_def}_password"
echo 'Enter your desired root name for the environmental variables'
echo 'that store your database username and password:'
read env_var_root_sel
env_var_root=$(anti_blank $env_var_root_sel $env_var_root_def)

env_var_username="${env_var_root}_username"
env_var_password="${env_var_root}_password"

#######################################################################################
# FINISHED: setting names of environment variables (env_var_username, env_var_password)
#######################################################################################

db_username_def="username_${db_root}"
echo
echo "Default username: ${db_username_def}"
echo 'Enter the desired username for the database:'
read db_username_sel
db_username=$(anti_blank $db_username_sel $db_username_def)

db_password_def="long_way_stinks"
echo
echo "Default password: ${db_password_def}"
echo 'Enter the desired password for the database:'
read db_password_sel
db_password=$(anti_blank $db_password_sel $db_password_def)

echo
echo "Database name (development): ${db_dev}"
echo "Database name (testing): ${db_test}"
echo "Database name (production): ${db_pro}"
echo
echo 'Environmental variable names'
echo "Username: ${env_var_username}"
echo "Password: ${env_var_password}"
echo
echo "Username: ${db_username}"
echo "Password: ${db_password}"

sh pg-start.sh

echo '--------------------------'
echo 'bundle install > /dev/null'
bundle install > /dev/null

ruby pg_setup.rb $db_dev $db_test $db_pro $env_var_username $env_var_password $db_username $db_password

sh git_check.sh
```

* Create the file pg_setup.rb with the following content:
```
#!/usr/bin/ruby

# rubocop:disable Metrics/LineLength
# rubocop:disable Style/PercentLiteralDelimiters
# rubocop:disable Style/UnneededPercentQ

require 'line_containing'
require 'remove_double_blank'
require 'string_in_file'

# Get input arguments (called by Bash script)
db_name_dev = ARGV[0]
db_name_test = ARGV[1]
db_name_pro = ARGV[2]
var_store_username = ARGV[3]
var_store_password = ARGV[4]
username_x = ARGV[5]
password_x = ARGV[6]

puts
puts '+++++++++++++++++++++'
puts 'Setting up PostgreSQL'

puts
puts '---------------------------------------------'
puts 'Setting up the PostgreSQL database parameters'
ENV['APP_DB_NAME_DEV'] = db_name_dev
ENV['APP_DB_NAME_TEST'] = db_name_test
ENV['APP_DB_NAME_PRO'] = db_name_pro
ENV['APP_DB_USER'] = username_x
ENV['APP_DB_PASS'] = password_x
system(%q{sudo -u postgres psql -c"CREATE ROLE $APP_DB_USER WITH CREATEDB LOGIN PASSWORD '$APP_DB_PASS';"})

# Development database
system(%q{sudo -u postgres psql -c"CREATE DATABASE $APP_DB_NAME_DEV WITH OWNER=$APP_DB_USER;"})
system('wait')

# Testing database
system(%q{sudo -u postgres psql -c"CREATE DATABASE $APP_DB_NAME_TEST WITH OWNER=$APP_DB_USER;"})
system('wait')

# Production database
system(%q{sudo -u postgres psql -c"CREATE DATABASE $APP_DB_NAME_PRO WITH OWNER=$APP_DB_USER;"})
system('wait')

puts '--------------------------------------------------'
puts 'Using Figaro to create initial configuration files'
system('rm config/application.yml')
system('figaro install')
system('wait')

puts
puts '---------------------------------'
puts 'Setting up config/application.yml'
open('config/application.yml', 'a') do |f|
  f.puts "\n\n"
  f.puts 'VAR_STORE_USERNAME: "USERNAME123"'
  f.puts "\n"
  f.puts 'VAR_STORE_PASSWORD: "PASSWORD123"'
end
StringInFile.replace('VAR_STORE_USERNAME', var_store_username, 'config/application.yml')
StringInFile.replace('USERNAME123', username_x, 'config/application.yml')
StringInFile.replace('VAR_STORE_PASSWORD', var_store_password, 'config/application.yml')
StringInFile.replace('PASSWORD123', password_x, 'config/application.yml')

puts
puts '------------------------------'
puts 'Setting up config/database.yml'
system 'cp config/database-pg.yml config/database.yml'
StringInFile.replace('VAR_STORE_USERNAME', var_store_username, 'config/database.yml')
StringInFile.replace('VAR_STORE_PASSWORD', var_store_password, 'config/database.yml')
StringInFile.replace('DB_NAME_DEV', ENV['APP_DB_NAME_DEV'], 'config/database.yml')
StringInFile.replace('DB_NAME_TEST', ENV['APP_DB_NAME_TEST'], 'config/database.yml')
StringInFile.replace('DB_NAME_PRO', ENV['APP_DB_NAME_PRO'], 'config/database.yml')

puts
puts '----------------'
puts 'Updating Gemfile'
LineContaining.delete_between_plus('# BEGIN: SQLite', '# END: SQLite', 'Gemfile')
RemoveDoubleBlank.update('Gemfile')

puts
puts '--------------------'
puts 'Database parameters:'
puts
puts "Name of database (development): #{db_name_dev}"
puts "Name of database (testing): #{db_name_test}"
puts "Name of database (production): #{db_name_pro}"
puts
puts "Environmental variable (stores username): #{var_store_username}"
puts "Environmental variable (stores password): #{var_store_password}"
puts
puts "Username: #{username_x}"
puts "Password: #{password_x}"
puts
puts 'The username and password of the databases are stored at config/application.yml.'
puts 'The database parameters are called at config/database.yml.'

puts
puts 'Finished setting up PostgreSQL'
puts '++++++++++++++++++++++++++++++'
puts

# rubocop:enable Metrics/LineLength
# rubocop:enable Style/PercentLiteralDelimiters
# rubocop:enable Style/UnneededPercentQ
```
* Do NOT run this script yet.  (You'll do this later.)
* Enter the following commands:
```
git add .
git commit -m "Added PostgreSQL setup scripts"
```
* Enter the command "git push origin 07-02-postgresql".

### Using the PostgreSQL Scripts
* The Generic App Template uses SQLite in the development environment.  These PostgreSQL scripts are provided to make it easy to switch from SQLite to PostgreSQL WITHOUT forcing the use of it.  Because executing these PostgreSQL scripts makes changes in the source code, you will NOT be entering any more Git commands in the rest of this chapter.
* Enter the command "sh pg_setup.sh".  To speed up the process, you can just use the default values given.  In the end, your app will use PostgreSQL instead of SQLite, and all of your tests should pass.
* Reset the development environment.  After you have downloaded the master branch of this app's code, enter the command "git checkout 07-02-postgresql".
* In one tmux window, enter the command "sh all.sh; sh server.sh".
* After the all.sh and server.sh commands have been executed in the first tmux window, go to a second tmux window and enter the command "sh pg_setup.sh".  Note that this script executes git_check.sh at the end.  All tests should pass, and there should be no issues raised by RuboCop or Rails Best Practices.
* Don't submit a pull request just yet.  You'll take care of this at the end of the next chapter.
* The next step is to view the PostgreSQL database.  That's what the next chapter is about.
