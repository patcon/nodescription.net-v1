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
drush dl drupal --drupal-project-rename=heroku-drupal
cd heroku-drupal
git init
git add .
git ci -m "Initial commit."
gem install heroku
heroku create --stack cedar
git push -u heroku master
heroku addons:add cleardb:ignite
cp sites/default/default.settings.php sites/default/settings.php
cat << EOH >> sites/default/settings.php
preg_match('|^mysql://(.*):(.*)@(.*)/(.*)\?reconnect=true$|', $_ENV['CLEARDB_DATABASE_URL'], $db_url_parts);

$databases = array (
  'default' =>. 
  array (
    'default' =>. 
    array (
      'database' => $db_url_parts[4],
      'username' => $db_url_parts[1],
      'password' => $db_url_parts[2],
      'host' => $db_url_parts[3],
      'port' => '', 
      'driver' => 'mysql',
      'prefix' => '', 
    ),  
  ),  
);
EOH
git add .
git ci -m "Added db credentials."
git push
```

Now visit the site and you should be able to run though the install process to build the database.

## Ideas

- Use php-build and php-version to pick the php version to build in the buildpack via and envvars:
https://github.com/CHH/php-build
https://github.com/wilmoore/php-version

- Use a custom buildpack to run drush make appropriately and generate site from install profile.
  - Can get commit hash for build scripting in `$_ENV['COMMIT_HASH']`:
  http://stackoverflow.com/questions/1582783/heroku-display-git-revision-hash-and-timestamp-in-views
- Add mbstring support via buildpack (Drupal warns): 