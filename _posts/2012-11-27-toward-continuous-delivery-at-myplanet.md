---
title: Toward Continuous Delivery at Myplanet
layout: default
published: false
---

# Toward Continuous Delivery at Myplanet

## Install Profiles

- We supplement the traditional install profile layout with everything else we can get into code. (tests, scripts, docs, unique configuration files the rest of the toolchain). Everything from deploy scripts to jenkins config files should be accessible to the developers working on a project. We're even exploring a tool called Sprint.ly which can act as a thin wrapper around your repo, which essentially populates and manages project management data primarily using repo commit message data.
- We believe in using base profiles.
- Allow us to develop consistently without passing database dumps around. The provides amazing freedom.
- Build app-side of development environment from the ground up using API's each time we start a story.
- Everything locked down in makefile and completely reproducible. We can use the profile to generate the site as it existed three months ago, and have it reproduced identically plus have its contemporary test suite pass (at least if it passed originally).

## Build Pipeline

- We are constantly building out our deployment pipeline. This is currently visualized on Jenkins, illustrating the path of each commit on its way to the production environment. The bulk of each step's logic resides in the repo's scripts themselves, so in theory, we even try to remain loosely coupled to Jenkins itself, so we could switch to another build pipeline tool should we later desire.
- Groups disparate data together for each commit (Github links, code review, test results, console logs of each deploy, progress of each commit through each stage of pipeline)

## Automation with Chef
(Or, Automating the S#%* Out of our Toolchain Setup)

- Ariadne to try to lock out local dev environment as closely to production as possible.
- Make every aspect of the dev environment for each project reproducible from a couple
- Jenkins environment is totally rebuildable. We could blow away a development Jenkins environment, and bring it back without any affect (except the lost history of test results and console logs, but we're working on that).

## Avoiding Vendor Lock-in

- Jenkins, Acquia, Drupal,
- We try to be tool agnostic. General scripts that can be run anywhere, with simpler wrapper scripts intended to be run on jenkins.