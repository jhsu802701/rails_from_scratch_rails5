# Unit 11
## Chapter 3: To Do List

* Create the file notes/TO_DO.txt with the following contents:
```
GETTING STARTED
* Replace instances of "Generic App Template" with the title of your app.
* Replace instances of "somebody@rubyonracetracks.com" with the email address you wish to use as the source of this app's email messages and the email address listed on the Contact page.
* Update the footer, header, and the rest of the layout.
* Update the static pages.

SHORT-TERM MAINTENANCE
* Specify the Ruby version of this app in the Gemfile.
* Create a custom Docker image for this project.  The initial Docker image should include the version of Ruby you are currently using in this app.  When a new version of Ruby becomes available, update your Docker image to include this new version.
* For each version of Ruby preinstalled in your Docker image, you should also include the mailcatcher gem and the current and expected future versions of the rails gem, the pg gem, the nokogiri gem, and any other gems that take a long time to install.  This makes it easier to upgrade the Gemfile.  (And if the upgrades require more work than anticipated, you can stick to the old versions until you resolve the upgrade issues.)

LONG-TERM MAINTENANCE
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
