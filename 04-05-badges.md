# Unit 4
## Chapter 5: Adding Badges

### Updating the README.md file
* Replace the README.md file with the following (providing the badge URLs in the appropriate locations):
```
# GENERIC APP TEMPLATE

BEGIN: continuous integration badges (markdown format)

Insert Circle CI build badge here
Insert Gemnasium badge here
Insert Hakiri badge here
Insert Code Climate GPA badge here

END: continuous integration badges (markdown format)

Welcome to Generic App Template!
This is a template created by following the 
[Rails From Scratch] (http://www.rubyonracetracks.com/rails_from_scratch.html) protocol.
This template is used by the 
[GenericApp] (https://github.com/jhsu802701/generic_app)
gem for starting off a new app by copying a high-quality pre-existing app.
This saves time, because it's MUCH quicker and easier to modify an existing app 
than it is to create a new one completely from scratch.
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "New README.md page with badges"
```

### Wrapping Up
Enter the following commands
```
git push origin master
sh heroku.sh
```

### Conclusions
* When you evaluate a pull request, check the test results provided before accepting it.
* After you update the master branch, check the status badges on your app's GitHub page.
* If the Circle CI build badge shows that your build failed even though it didn't fail in your local development environment, this is a red flag warning you of a potential dependency mismanagement issue.
