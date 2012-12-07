---
title: Deploying Drupal Install Profiles to Heroku
layout: default
published: false
---

# Deploying Drupal Install Profiles to Heroku

Having lived in Drupal Narnia for the past 5 years, I'd often heard buzzwords drift in from other communities. One such buzzword was "Heroku", which was some used for deploying apps. I didn't know much about how it worked, but heard that it somehow involved "dynos" and "slugs". I didn't know what these things meant, but since Heroku was for deploying and hosting ruby apps, I didn't particularly care.

In the past year or so, I've started to use some ruby tools with Drupal, and I've gotten comfortable with the ecosystem and started to dive a little deeper into services like Heroku. In a nutshell, the Heroku of 2007 was to the ruby world what Acquia and Pantheon are to the Drupal world -- each was a Platform as a Service (PaaS) intended to host a certain type of application.

Heroku operates in a manner more akin to Pantheon rather than Acquia. Whereas Acquia tends to treat servers (shared or dedicated) as the first-class citizens in their model, Heroku and Pantheon treat "processes" as the first-class citizens. I'm still coming to grips with the finer points, but the general difference is that to scale up an application on Acquia, you pay for more servers on ManagedCloud or upgrade from DevCloud where you had a slice of a shared server. With Pantheon/Heroku, you scale by added more processes, and the processes dedicated to your application need not all be run on the same server over the lifetime of your application. It's this latter model that has made it so simple for Pantheon and Heroku to offer free application hosting -- if an app is inactive, the PaaS can intelligently decide to stop running those processes, and free up those resource until a visitor comes knocking. Pantheon has [a great write-up on how they do it.](https://www.getpantheon.com/news/inside-pantheon-drops)

I'll take a sec to explain the important parts of Heroku's deployment workflow. Much like Acquia and Pantheon, you deploy by pushing to a git repo, for which each code push kickstarts a deploy process. Heroku works with many languages now, but all have a similar workflow: The developer stores a build file in the root of their application, and during each push, Heroku uses the appropriate package manager to read this build file and pull in all dependencies required to deploy the app. The analogous components in the Drupal developer's toolkit would be Drush Make building its `build.make` file (or perhaps Composer and `composer.json`, if you're keeping up with the Joneses). Consequently, since dependencies are pulled in and built by the package manager on each deployable git push, Heroku users don't tend to include dependant packages and libraries in their version control system. They instead choose to reduce all that "code noise" of those libraries into a flat build file, and treat each library as a black box unless they find the need to hack it.

So this brings us to today, where Heroku and the market in general seem to be moving toward a totally language and application-agnostic model that might soon threaten (or at least put pressure), on niche players like Acquia and even the more future-ready Pantheon. Whereas in the past, Heroku's internal code was geared to launching ruby apps, they now serve apps written in Python, Clojure, Node.js, and (at least unofficially) PHP apps. What's more, the setup of each of these environments is *totally programmable*. Let me explain this a little more fully, because it's honestly the most important part.

TODO:
- Buildpacks (Stackato using Heroku buildpacks)
  - Custom buildpacks allow us to create our own community build processes
- CloudFoundry apps are now deployable across any cloudfoundry PaaS
- Explain the heroku CLI, envvars, and addon ecosystem

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

- Law of the Instrument FTW: http://en.wikipedia.org/wiki/Law_of_the_instrument

## Ideas

- Use php-build and php-version to pick the php version to build in the buildpack via and envvars:
https://github.com/CHH/php-build
https://github.com/wilmoore/php-version

- Use a custom buildpack to run drush make appropriately and generate site from install profile.
  - Can get commit hash for build scripting in `$_ENV['COMMIT_HASH']`:
  http://stackoverflow.com/questions/1582783/heroku-display-git-revision-hash-and-timestamp-in-views
- Add mbstring support via buildpack (Drupal warns).
- Perhaps can even add hooks like python buildpack did: https://github.com/heroku/heroku-buildpack-python/blob/master/bin/steps/hooks/pre_compile
- Talk about vulcan and how it makes it simple to run Drupal on whatever system-level tools your heart desires. Want a specific version of PHP? Of apache? Vulcan makes it simple to compile these tools on the heroku stack itself, then save them to an Amazon S3 storage bucket where your compile script can pull it in on each push: http://www.ryandaigle.com/a/using-vulcan-to-build-binary-dependencies-on-heroku