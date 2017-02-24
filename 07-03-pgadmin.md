# Unit 7
## Chapter 3: pgAdmin

pgAdmin is a GUI browser tool that you can install in your host environment to view your PostgreSQL database for your Ruby on Rails app.  It's analogous to the SQLiteBrowser software you used earlier for examining the SQLite database.

### What's the point?
* The ability to view the database contents directly is important for troubleshooting.
* Viewing the PostgreSQL database by manually entering commands is frustrating.  Every time I had to do this, I had difficulty figuring out what to do, and I frequently messed up.
* The pgAdmin GUI browser makes viewing the database contents a much easier process.

### Downloading and Installing pgAdmin
* If you have already installed pgAdmin on your host system, you may move on to "The PostgreSQL Setup".
* If your host environment is Debian Linux, Ubuntu Linux, or one of their derivatives, enter the command "sudo apt-get install pgadmin3" in the HOST environment. pgAdmin will install in a few minutes.
* If your host environment is OS X or Windows, go to the [pgAdmin page](https://www.pgadmin.org/). Follow the instructions for downloading and installing this software.

### The PostgreSQL Setup
* At this point, your app should be set up with PostgreSQL instead of SQLite for the development environment database.
* Enter the command "sh seed.sh".  (NOTE: You should seed the database BEFORE using pgAdmin to connect to it.  If you seed the database when you already have an active connection through pgAdmin, you may get an error message warning you that the database is being accessed by others.)
* Get the development database name from the config/database.yml file.
* Open the file config/application.yml.  Get the username and password from it.  The format of the contents of this file is:
```
# Comments

(name of variable containing username): "(username)"
(name of variable containing password): "(password)"
```

### Viewing the Database
* From your host system, open the ports.txt file within the shared directory of the Docker container.  (Alternatively, you can enter the command "cat /home/winner/shared/ports.txt" from your Docker container.)  Note the host port that corresponds to port 5432 in the Docker port.  That's the port number to enter in your web browser in order to view the app.
* After the seeding process has finished, open pgAdmin.
* Add a server with the following parameters
  * Name: generic_rails
  * Host: localhost (or the IP address of Docker Machine)
  * Port: (host port corresponding to port 5432 in Docker)
  * Maintenance DB: postgres
  * Username: (from config/application.yml)
  * Password: (from config/application.yml)
* In the object browser, go to Server Groups -> Servers -> generic_rails -> Databases -> (development database name) -> Schemas -> public -> Tables.
* In pgAdmin, select the admin table and then right-click -> View Data -> View All Rows.  You'll see the table containing all of the admin data.
* Repeat the above step for users.  You'll see the table containing all of the user data.

### Wrapping Up
* Go to the GitHub repository and click on the "Compare and pull request" button for the 07-02-postgresql branch, which you pushed in the previous chapter.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
