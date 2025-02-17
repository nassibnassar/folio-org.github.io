---
layout: page
title: Using a FOLIO VM - Overview
permalink: /tutorials/folio-vm/overview/
menuInclude: yes
menuLink: yes
menuTopTitle: Tutorials
---

## Goals

* Explain how to utilise a FOLIO virtual machine (VM).

## Prerequisites

* [Vagrant](https://www.vagrantup.com/) -- Recent version.
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads) --
Determine the version of VirtualBox to [match](https://www.vagrantup.com/docs/virtualbox) your version of `vagrant`.
* [curl](https://curl.haxx.se) -- Only 'curl' commands are shown in this tutorial.
To use curl to interact with Okapi from outside the VM, of course curl is needed on the host system.
Note that Vagrant has a packaged curl, so that can be used from within the VM.
Easier-to-use interactive and command-line tools are available (see FAQ [How to use APIs](/faqs/how-to-use-apis/)).

## Background

Each night the FOLIO [reference environments](/guides/automation/#reference-environments) are rebuilt using the current state of development.

The continuous integration also constructs various Vagrant boxes.
These are [available](https://github.com/folio-org/folio-ansible/blob/master/doc/index.md#prebuilt-vagrant-boxes) for local use, providing a ready-to-run self-contained operating system environment and up-to-date FOLIO instance.
(Note that that link is only for explaining the various boxes.
The first Lesson of this Tutorial will establish one for local use.)

<a id="old-vm"></a>This tutorial will only focus on the `folio/release-core` VM.
If your host machine has sufficient resources available (e.g. memory) then it would be possible to use the `folio/release`
(i.e. for the most recent "[flower release](/guides/regular-releases/)")
or the `folio/snapshot` VM instead, which have the full set of FOLIO apps.

<div class="attention">
Note: Due to the discontinuation of snapshot-core
(<a href="https://issues.folio.org/browse/FOLIO-3292">FOLIO-3292</a>)
and the platform-minimal has not yet happened
(<a href="https://issues.folio.org/browse/FOLIO-3253">FOLIO-3253</a>),
this tutorial still refers to "release-core".
That is for the Juniper R2-2021 release -- subsequent flower releases do not have a release-core.
</div>

## Troubleshooting and known issues

There are [troubleshooting](https://github.com/folio-org/folio-ansible/blob/master/doc/index.md#troubleshootingknown-issues) notes in the folio-ansible repository, so generally those will not be repeated here.

## Lessons and steps

Follow each lesson sequentially:

1. [Create workspace and launch VM](../01-create-workspace/) and update box.
1. [VM system overview](../02-system-overview/) and viewing logfiles.
1. [Interact with the FOLIO VM](../03-interact/).
1. [Conduct local development](../04-local-development/).

