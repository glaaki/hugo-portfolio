---
title: "Config - Be Explicit"
date: 2019-08-26T15:38:59-05:00
---

# The Motivation:
We rely on ansible for some deploys & to enforce certain state at our retail locations; I was burned by an upgrade to this system last week and thought it would be nice to talk about what happened and what could have been done to prevent it.

# The Incident:
I built some tooling around automated playbook retries because the connection to some of our hosts is pretty bad (3G levels of bad...); across ~5500 hosts something is almost guaranteed to fail. In a nutshell my wrapper takes an inventory and playbook name, checks a specific directory for any retry files (which would imply that playbook had already run) and runs the playbook against any remaining hosts.

I noticed that these playbooks were running against _all_ the hosts every time; but couldn't immediately figure out why. All the directories looked fine, correct permissions, etc. I examined the config file and `retry_files_save_path` was what I expected, so nothing jumped out as an obvious issue. Running ansible with `-vvv` didn't give any clues. There was no mention of retry files at all, which was strange because I expected at least _some_ kind of error.

![ugh](/stock/ugh.jpg "Photo by Nathan Dumlao on Unsplash")

# The Problem:
After digging into the docs for an embarassingly long time, I realized a default had changed in a recent ansible version. `retry_files_enabled` wasn't in our ansible.cfg at all because `retry_files_enabled=True` was the default, but this newer version of ansible defaulted to `retry_files_enabled=False` instead.

# What I Learned:
I was implicitly relying on some behavior that changed and didn't catch it in the changelogs. If you rely on some specific behavior of a system, be explicit & put it in the config. If I wanted to generalize here, I would say it's good practice to think about what assumptions you are making about environment and find some way to explicitly enforce them.
