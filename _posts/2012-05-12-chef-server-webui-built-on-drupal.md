---
title: toward a chef-server web UI built on drupal
layout: default
published: false
tags: [devops, chef, nerd, drupal]
---

# toward a chef-server web UI built on drupal

So ever since I first started using Chef at [Myplanet Digital](http://myplanetdigital.com/careers), I've been enamoured with the idea of using Drupal to power the WebUI. Chef, as you may or may not know, brings all its data structures into and out of it's system in JSON format through its REST API. Most notable among these data structures are roles (groups of recipes and attributes) and databags (containers for arbitrary data structures). Databags are really the wildcard, as you can use them in any number of ways, and they can form the backbone of your infrastructure. These data structures that live on the chef server are usually stored elsewhere in a VCS repo as files, written in either Chef's Ruby DSL or in straight-up JSON files.

I would say it makes sense to manipulate roles only via direct file changes, but databags are by nature intended to be much more transient. For example, you might want to create a "users" databag, which would have an item for each user in your organization, complete with any metadata you see fit to manage access, preferences, and interaction with external systems. Or perhaps you might want an "apps" databag that somehow helps manage all your applications. The point is that you might want these arbitrary datastore objects to have a nice and pretty UI, where your organization can manipulate them in various ways without manipulating files in VCS. (This isn't to say that commiting the UI-side changes back into VCS should be part of your process, and it could certainly be done with JSON files.)

The Chef WebUI does have a rudimentary JSON editor, but it's basically just an interactive and expandable nested array element -- there's no selection helpers or validation, unless you count a failure on the next chef run.

So the idea is that if you built the UI with something like Drupal, it could be fairly accessible for folks to dive in and create data structures with enforced relations, helper widgets, and things of that nature. So for instance, someone could map a "users" databag to the user entity, and add fields to those users through the UI for users with the "developer" role on the site -- providing text areas for public SSH keys, UNIX passwords, email addresses, and teams. In fact, the "team" field could easily involve autoselection based on a "team" entity type, or could perhaps be pulled in from the GitHub API using Feeds module, based on another "user" field for "github_username". There's just a lot of potential to use all the service-y type functionality of Drupal to coordinate an infrastructure even among those who aren't especially Chef-minded.

So chef-server has a REST API, but also has a CLI tool called knife, which can be used to upload JSON files through that same API by just pointing it to the JSON file representation of the data structure that should be uploaded. So we have two possible appoaches:

Interact with the REST client directly via Drupal, including authentication and communication. (hard)
Have Drupal write JSON files to the filesystem, and have a script run on cron to kick knife into action when files update. (easier)
Keep in mind, this effort will be totally proof-of-concept, and in no way am I going to tackle this from a security-minded perspective, at least intially.

### Method 1: Quick-and-Dirty JSON

If I go this route, this is where I might start, just to test how the Drupal interface might fit together.

- http://drupal.org/node/903528

### Method 2: The Real Deal RESTful

#### Chef Docs

- http://wiki.opscode.com/display/chef/Server+API
- http://wiki.opscode.com/display/chef/Making+Authenticated+API+Requests

#### Some Drupal-ish Things to Extend

[HTTP Client Drupal Module (extendable)](http://drupal.org/project/http_client)

[OAuth Authentication example of module extension](http://drupalcode.org/project/http_client.git/blob/refs/heads/7.x-2.x:/includes/HttpClientOAuth.inc)

#### Examples in the Wild

[Python](https://github.com/coderanger/pychef/tree/master/chef)

[Low-level Ruby](https://github.com/opscode/mixlib-authentication/tree/master/lib/mixlib/authentication)

[High-level Ruby](https://github.com/danryan/spice/tree/master/lib/spice)

#### A Decent Start

[Rough, unsupported PHP lib](https://gist.github.com/a52c932629d6a044716d#nogist)

[Easiest way to spin up a chef-server](https://github.com/patcon/chef-hatch-repo)