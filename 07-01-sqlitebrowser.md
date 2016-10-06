# Unit 7
## Chapter 1: SQLite Browser

SQLiteBrowser is a GUI browser tool that you can install in your host environment to view your SQLite database for your Ruby on Rails app.  It's MUCH easier to use SQLite Browser than to remember commands.  When your app does not work as expected, SQLite is an essential troubleshooting tool for checking the contents of the databases involved.

### Downloading and Installing SQLiteBrowser
* If your host environment is Debian Linux, Ubuntu Linux, or one of their derivatives, enter the command "sudo apt-get install sqlitebrowser".  SQLite Browser will install in a few minutes.
* If your host environment is OS X or Windows, go to the [DB Browser for SQLite](http://sqlitebrowser.org/) page.  Follow the instructions for downloading and installing SQLite Browser.

### Viewing the Database
In SQLiteBrowser, just open the file db/development.sqlite3 file within your project.  You can now view the admin and user data that you seeded.
