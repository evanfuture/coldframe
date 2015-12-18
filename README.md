# Coldframe

Coldframe is, as its metaphorical title implies, a frame in which to grow and nurture new websites.  With a few simple commands, you can be up and running a development environment on your machine that mirrors your production server.

Modelled off of [trellis](https://github.com/roots/trellis "End-to-end automated WordPress environment management via Ansible."), from the amazing team at [Roots](https://roots.io/ "Roots.io"), Coldframe is a less *thorough* solution.  The framework is made for ease of use and tinkering, rather than for full best practice and security.  This integration will happen in the future, but we all have work to do in the present, so I’m making Coldframe available to start using now.

**Note: This document will only be considered complete when this line is removed.**

## Requirements
- Ansible
- Virtualbox
- Vagrant
- Working SSH Keys for a bitbucket account
- Node/npm
- Coldframe-cli (coldframe’s own node module for simple command line usage)
- There might be more…

## Features
- Forthcoming.

## Getting Started
Begin with: `npm install coldframe-cli -g`

This will install our command line utility and allow you to get started very quickly.  Once you’re installed you can:

### *coldframe init*
This will setup your box for the first time.  I’ve tried to make it idempotent, so it won’t overwrite anything if you run it a second time.

A secrets file will be created, and a user secrets.  This allows you to store your bitbucket passwords for both your team and yourself, so you can run your own private projects on your dev box in addition to your team’s.

Running your own will require you to login to the bitbucket website to share with the team.
### *coldframe cycle*
This is a shortcut for running *vagrant halt && vagrant up*.  Some of the other commands use it internally, such as *activate*

### *coldframe activate [site_slug]*
This will create a new site, or download an existing site, depending on whether you already have a hostfile.

## Help
In all cases, these commands sometimes hang.  VirtualBox and Vagrant both do their best to work together, but the system isn’t perfect yet, and in the meantime, we struggle through.

Almost always, once the process times out, you can fix things with *coldframe cycle*.  If this isn’t working, try it manually, with *vagrant halt* followed by *vagrant up*.  As a last resort, you can manually shut down the box from VirtualBox’s UI.
