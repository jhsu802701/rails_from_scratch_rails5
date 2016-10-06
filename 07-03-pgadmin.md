# Unit 7
## Chapter 3: pgAdmin

pgAdmin is a GUI browser tool that you can install in your host environment to view your PostgreSQL database for your Ruby on Rails app.  It's analogous to the SQLiteBrowser software you used earlier for examining the SQLite database.

### Downloading and Installing pgAdmin
* If your host environment is Debian Linux, Ubuntu Linux, or one of their derivatives, enter the command "sudo apt-get install pgadmin3". pgAdmin will install in a few minutes.
* If your host environment is OS X or Windows, go to the [pgAdmin page](https://www.pgadmin.org/). Follow the instructions for downloading and installing SQLite Browser.

### Setting up PostgreSQL
* Enter the command "sh pg_setup.sh".
* Open the file config/application.yml.  Get the username and password from it.  The format of the contents of this file is:
```
# Comments

(name of variable containing username): (username)
(name of variable containing password): (password)
```

### Viewing the Database
* Open pgAdmin.
* Add a server with the following parameters
  * Name: localhost
