# WordPress Heroku

This project is a template for installing and running [WordPress](http://wordpress.org/) on [Heroku](http://www.heroku.com/) with MySQL (on Amazon RDS, for example). The repository comes bundled with [WP Read-Only](http://wordpress.org/extend/plugins/wpro/). This setup is mostly a riff on [mhoofman's wordpress-heroku](https://github.com/mhoofman/wordpress-heroku)

## Installation

Clone the repository from Github

    $ git clone git://github.com/andresdouglas/wordpress-heroku-mysql.git

With the [Heroku gem](http://devcenter.heroku.com/articles/heroku-command), create your app

    $ cd wordpress-heroku-mysql
    $ heroku create
    Creating strange-turtle-1234... done, stack is cedar
    http://strange-turtle-1234.herokuapp.com/ | git@heroku.com:strange-turtle-1234.git
    Git remote heroku added

By now you should have created a MySQL DB somewhere. If you've chosen RDS,
here's what you have to do in order to add it (taken from this [StackOverflow question](http://stackoverflow.com/questions/9741072/specify-ssl-for-heroku-external-mysql-database-connection/19831204#19831204))

Download the [Amazon RDS CA
certificate](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html#MySQL.Concepts.SSLSupport) and bundle it with your app.

    $ curl https://s3.amazonaws.com/rds-downloads/mysql-ssl-ca-cert.pem > ./config/amazon-rds-ca-cert.pem

Add the file to git, and redeploy to Heroku.

Change DATABASE_URL to pass sslca:
   
    $ heroku config:add DATABASE_URL="mysql2://username:password@hostname/dbname?sslca=config/amazon-rds-ca-cert.pem -a <app_id>

Use Heroku legacy PHP buildpack:

    heroku config:set BUILDPACK_URL=https://github.com/heroku/heroku-buildpack-php#legacy

Add the ability to send email (i.e. Password Resets etc)

    $ heroku addons:add sendgrid:starter
    Adding sendgrid:starter on your-app... done, v14 (free)
    Use `heroku addons:docs sendgrid` to view documentation.

Create a new branch for any configuration/setup changes needed

    $ git checkout -b production

Store unique keys and salts in Heroku environment variables. Wordpress can provide random values [here](https://api.wordpress.org/secret-key/1.1/salt/).

    heroku config:set AUTH_KEY='put your unique phrase here' \
      SECURE_AUTH_KEY='put your unique phrase here' \
      LOGGED_IN_KEY='put your unique phrase here' \
      NONCE_KEY='put your unique phrase here' \
      AUTH_SALT='put your unique phrase here' \
      SECURE_AUTH_SALT='put your unique phrase here' \
      LOGGED_IN_SALT='put your unique phrase here' \
      NONCE_SALT='put your unique phrase here'

Deploy to Heroku

    $ git push heroku production:master
    -----> Heroku receiving push
    -----> PHP app detected
    -----> Bundling Apache v2.2.22
    -----> Bundling PHP v5.3.10
    -----> Discovering process types
           Procfile declares types -> (none)
           Default types for PHP   -> web
    -----> Compiled slug size is 13.8MB
    -----> Launcing... done, v5
           http://strange-turtle-1234.herokuapp.com deployed to Heroku

    To git@heroku:strange-turtle-1234.git
      * [new branch]    production -> master

After deployment WordPress has a few more steps to setup and thats it!

## Usage

Because a file cannot be written to Heroku's file system, updating and installing plugins or themes should be done locally and then pushed to Heroku.

## Updating

Updating your WordPress version is just a matter of merging the updates into
the branch created from the installation.

    $ git pull # Get the latest

Using the same branch name from our installation:

    $ git checkout production
    $ git merge master # Merge latest
    $ git push heroku production:master

WordPress needs to update the database. After push, navigate to:

    http://your-app-url.herokuapp.com/wp-admin

WordPress will prompt for updating the database. After that you'll be good
to go.

## Deployment optimisation

If you have files that you want tracked in your repo, but do not need deploying (for example, *.md, *.pdf, *.zip). Then add path or linux file match to the `.slugignore` file & these will not be deployed.

Examples:
```
path/to/ignore/
bin/
*.md
*.pdf
*.zip
```

## Wiki

* [Custom Domains](https://github.com/mhoofman/wordpress-heroku/wiki/Custom-Domains)
* [Media Uploads](https://github.com/mhoofman/wordpress-heroku/wiki/Media-Uploads)
* [Setting Up a Local Environment on Linux (Apache)](https://github.com/mhoofman/wordpress-heroku/wiki/Setting-Up-a-Local-Environment-on-Linux-(Apache\))
* [Setting Up a Local Environment on Mac OS X](https://github.com/mhoofman/wordpress-heroku/wiki/Setting-Up-a-Local-Environment-on-Mac-OS-X)
* [More...](https://github.com/mhoofman/wordpress-heroku/wiki)
