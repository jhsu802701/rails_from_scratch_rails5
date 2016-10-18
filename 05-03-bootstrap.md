# Unit 5
## Chapter 3: Bootstrap Styling

In this chapter, you will add Bootstrap styling to give your site a much more polished appearance.

### New Branch
Enter the command "git checkout -b 05-03-bootstrap".

### Gemfile
* Add the line "gem 'bootstrap-sass'" to the end of the Gemfile.
```
gem 'bootstrap-sass' # Bootstrap styling
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Installed bootstrap-sass"
```
### Configuring Bootstrap
* Create the file app/assets/stylesheets/custom.scss with the following code:
```
@import "bootstrap-sprockets";
@import "bootstrap";

@mixin box_sizing {
  -moz-box-sizing:    border-box;
  -webkit-box-sizing: border-box;
  box-sizing:         border-box;
}

/* universal */

body {
  padding-top: 60px;
}

section {
  overflow: auto;
}

textarea {
  resize: vertical;
}

.center {
  text-align: center;
}

.center h1 {
  margin-bottom: 10px;
}

/* typography */

h1, h2, h3, h4, h5, h6 {
  line-height: 1;
}

h1 {
  font-size: 3em;
  letter-spacing: -2px;
  margin-bottom: 30px;
  text-align: center;
}

h2 {
  font-size: 1.2em;
  letter-spacing: -1px;
  margin-bottom: 30px;
  text-align: center;
  font-weight: normal;
  color: #777;
}

p {
  font-size: 1.1em;
  line-height: 1.7em;
}

/* header */

#logo {
  float: left;
  margin-right: 10px;
  font-size: 1.7em;
  color: #fff;
  text-transform: uppercase;
  letter-spacing: -1px;
  padding-top: 9px;
  font-weight: bold;
}

#logo:hover {
  color: #fff;
  text-decoration: none;
}

/* footer */

footer {
  margin-top: 45px;
  padding-top: 5px;
  border-top: 1px solid #eaeaea;
  color: #777;
}

footer a {
  color: #555;
}

footer a:hover {
  color: #222;
}

footer small {
  float: left;
}

footer ul {
  float: right;
  list-style: none;
}

footer ul li {
  float: left;
  margin-left: 15px;
}

/* miscellaneous */

.debug_dump {
  clear: both;
  float: left;
  width: 100%;
  margin-top: 45px;
  @include box_sizing;
}
```
* Stop and restart the Rails server.  (If you refresh your local web page in your browser without restarting the Rails server, you will get a syntax error message.)
* View your site in your local browser.
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Configured Bootstrap styling"
```
### Wrapping Up
* Enter the command "git push origin 05-03-bootstrap".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
* The new static pages should now appear on the Heroku site.
