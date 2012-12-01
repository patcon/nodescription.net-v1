---
title: Toward Continuous Delivery at Myplanet
layout: default
published: false
---

# Toward Continuous Delivery at Myplanet

Here at Myplanet, the organization that synchronizes my circadian rhythm for awesome, we're working on applying to Drupal some of the principles from the wider realms of software development. In particular, we're trying to wrestle Drupal into conforming to the *continuous delivery* paradigm. What's this continuous delivery thing, you ask? The dictionary defines continuous delivery as "the uninterrupted carrying and turning over of letters to a designated recipient or recipients." Of course, this is stupidly incorrect. Dictionaries are ancient creatures that cannot keep up with the pace of the internet and its multitudinous legions of cats and dubstep remixes.

So what is this thing, as it related to software like Drupal? It's basically an extension of *continuous integration*, which is much more familiar in the ears of many developers. While continuous integration tends to focus on merging and testing the code of diverse team-members as early and often as possible, continuous delivery sets it sights on deploying code to production as early and often as possible. But even if you're not ready to take this methodology to its logical conclusion, there's much to be learned in the methodologies of the [book that constitutes the Continuous Delivery bible](http://www.amazon.com/dp/0321601912?tag=contindelive-20).

We've been building out our infrastructure and process for the past year or so, but the pieces have really come into their own in our most recent project, _________. I'll leave the explanation of the finer points of continuous delivery for another time, but I'd like to take a moment to elaborate on how we've applied its general principles to the development of the ____________ Drupal site.

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
(Or, Automating the S#%\* Out of our Toolchain Setup)

- Ariadne to try to lock out local dev environment as closely to production as possible.
- Make every aspect of the dev environment for each project reproducible from a couple
- Jenkins environment is totally rebuildable. We could blow away a development Jenkins environment, and bring it back without any affect (except the lost history of test results and console logs, but we're working on that).

## Avoiding Vendor Lock-in

- Jenkins, Acquia, Drupal,
- We try to be tool agnostic. General scripts that can be run anywhere, with simpler wrapper scripts intended to be run on jenkins.

Hopefully it's coming across how excited we are about all this stuff. I'm assuming that ol' Nate-O, our resident content strategist, will have donned his Editor hat and culled down my superlative count by 80%, but hopefully it's still coming across how excited we are about all this stuff. *[Editor's note: While it's true tht Patrick may use more superlatives than anyone I know, I've left his enthusiasm intact. Also, stop calling me "Nate-O".]* Personally, the thing I'm most excited about is the fact that, at every level, the folks at Myplanet are all excited to share our code and processes early and often. We don't define success as hoarding effective processes, but rather in sharing them as widely as possible.

Oh, and in case you happen to be a Drupal developer with your eyes peeled for the next great opportunity, I was supposed to mention one more thing: Nate smells. *[Editor's Note: What Patrick was supposed to mention was that we're hiring. And no I don't.]*