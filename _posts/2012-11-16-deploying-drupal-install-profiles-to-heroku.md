---
title: Deploying Drupal Install Profiles to Heroku
layout: default
published: false
---

# Deploying Drupal Install Profiles to Heroku

- http://sysadminsjourney.com/content/2011/09/20/drupal-heroku/
- https://devcenter.heroku.com/articles/buildpacks
- https://github.com/heroku/heroku-buildpack-php/
- http://bergie.iki.fi/blog/using_composer_to_manage_dependencies_in_heroku_php_apps/

Hoping to create a buildpack for deploying a drupal install profile, installing drush make and using it to rebuild the site each time:
https://github.com/patcon/heroku-buildpack-php-drupal

For now, just trying to get Drupal working with some of the free heroku add-ons:

```
# Install Heroku CLI gem
# You may alternatively use the Heroku Toolbelt installer:
# https://toolbelt.heroku.com/
gem install heroku

# Clone minimal install profile with appropriate structure
git clone https://github.com/patcon/heroku-drupal-profile.git
cd heroku-drupal-profile

# Create the Heroku app and push your code
heroku create --stack cedar --buildpack https://github.com/patcon/heroku-buildpack-php-drupal.git
git push -u heroku master
# Watch the output as your app is deployed to a url like:
# http://YOUR_APP_NAME.herokuapp.com/install.php

# Scale up to one webserver "dyno".
# A "dyno" is a term in heroku's process model of deployment.
heroku scale web=1
```

Now visit the site's `install.php` page and you should be able to run though the install process to build the database.

http://YOUR_APP_NAME.herokuapp.com/install.php

- To blow away the database so you can run through the installer again (presuming you're building your site as an install profile):

        heroku addons:remove cleardb:ignite --confirm YOUR_APP_NAME
        heroku addons:add cleardb:ignite

- If your DB is *empty* and you'd like to reinstall your site programmatically via drush:

        heroku run "cd www; LD_LIBRARY_PATH=../php/ext ./../php/bin/php -f ../drush/drush.php site-install minimal --php=../php/bin/php --yes --account-pass=admin"
        
## Ideas

- Use php-build and php-version to pick the php version to build in the buildpack via and envvars:
https://github.com/CHH/php-build
https://github.com/wilmoore/php-version

- Use a custom buildpack to run drush make appropriately and generate site from install profile.
  - Can get commit hash for build scripting in `$_ENV['COMMIT_HASH']`:
  http://stackoverflow.com/questions/1582783/heroku-display-git-revision-hash-and-timestamp-in-views
- Add mbstring support via buildpack (Drupal warns):