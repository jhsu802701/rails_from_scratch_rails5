# Chapter 11: PostgreSQL

### B. pg-start.sh
* Create the file pg-start.sh, enter the following contents, and save the file:
```
#!/bin/bash

echo '--------------'
echo 'sh pg-start.sh'
echo 'Starting PostgreSQL'
echo 'NOTE: This step is necessary if you are using PostgreSQL in a'
echo 'Docker development environment.'
echo 'If you are not using PostgreSQL in the development environment,'
echo 'you can skip this step to save time.'
sudo service postgresql start
```
* Enter the command "sh pg-start.sh" to run this command.  You'll see a system message notifying you that PostgreSQL is starting up.  If you decide to change your development and testing database from SQLite to PostgreSQL, starting up PostgreSQL will be necessary in a Docker container, because the PostgreSQL server does NOT automatically start.
* Enter the following commands:
```
git status
git add .
git commit -m "Added pg-start.sh"
