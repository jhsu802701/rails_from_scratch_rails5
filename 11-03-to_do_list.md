# Unit 11
## Chapter 3: To Do List

* Create the file README-to_do.txt with the following contents:
```
GETTING STARTED
* Run the credentials.sh script.
* Start a Git repository for this app, and push this app into that repository.
* Deploy this app to Heroku.  Instructions are provided below.
* Replace instances of "Generic App Template" with the title of your app.
* Replace instances of "somebody@rubyonracetracks.com" with the email address you wish to use as the source of this app's email messages and the email address listed on the Contact page.
* Update the footer, header, and the rest of the layout.
* Update the static pages.

HEROKU DEPLOYMENT
* Enter the command "heroku create".
* In your browser, visit your assigned URL. You will see the initial Heroku splash screen, because the Heroku server does not yet have your source code.
* Change your app's name (and URL) by entering the command "heroku rename (name)".
* In your browser, visit your new URL. You will again see the initial Heroku splash screen.
* To see which Heroku project you are currently configured to push to, enter the command "git remote -v".
* To deploy your app, enter the command "sh heroku.sh".

SHORT-TERM MAINTENANCE
* Specify the Ruby version of this app in the Gemfile.
* Create a custom Docker image for this project.  The initial Docker image should include the version of Ruby you are currently using in this app.  When a new version of Ruby becomes available, update your Docker image to include this new version.
* For each version of Ruby preinstalled in your Docker image, you should also include the mailcatcher gem and the current and expected future versions of the rails gem, the pg gem, the nokogiri gem, and any other gems that take a long time to install.  This makes it easier to upgrade the Gemfile.  (And if the upgrades require more work than anticipated, you can stick to the old versions until you resolve the upgrade issues.)

LONG-TERM MAINTENANCE
* Pin all gems in the Gemfile.
* Run the upgrade_gems.sh script.
* Upgrade the gem versions (other than rails/pg/nokogiri) in the Gemfile.
* Upgrade the Ruby version of this app and the Ruby versions preinstalled in the custom Docker image.
* Upgrade the rails/pg/nokogiri versions in the Gemfile and in the custom Docker image.
```
* Enter the following commands:
```
git add .
git commit -m "Added notes/TO_DO.txt"
git push origin master
sh heroku.sh
```
