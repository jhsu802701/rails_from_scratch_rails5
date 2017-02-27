# Unit 11
## Chapter 1: Admin Profile

In this chapter, you will create admin profile pages. In the interest of privacy, admin profiles will be visible ONLY to admins.

### New Branch
Enter the command "git checkout -b 11-01-admin_show".

### Wrapping Up
* Enter the command "git push origin 11-01-admin_show".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* The email display on the admin profile page will be flagged by Hakiri. Log into Hakiri and mark this as a false positive by clicking on Details -> 1 warning -> admin view -> 1 warning -> Mark as False Positive.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
