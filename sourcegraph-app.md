# sourcegraph-app

These are my instructions for running *Sourcegraph App* (the desktop app) on my personal computer.


## Overview

I've been able to get Sourcegraph App up and running but with some quirks. I'm getting the 'initial indexing in progress'
progress message and it never ends. Something is not quite working.

I'm running macOS Ventura (13.2.1) on an Apple Silicon Macbook.


## Instructions

These are the instructions I follow to get Sourcegraph App running on my computer.

1. Pre-requisite: Docker
    * Technically, Docker is an optional pre-requisite but I want the full Sourcegraph App experience.
2. Pre-requisite: the Sourcegraph CLI (`src`)
    * Technically, `src` is an optional pre-requisite but I want the full Sourcegraph App experience.
    * I installed it using HomeBrew. See other installation options at <https://github.com/sourcegraph/src-cli>.
    * Do I need to set the env vars, like `SRC_ENDPOINT`? Or shouldn't `Source Graph.app` know how to accomplish the same
      effect?
3. Install Sourcegraph App
    * I installed via the `.dmg` installer
4. Configure the PATH
    * (This was surprisingly tricky)
    * [The Sourcegraph App docs clearly state](https://docs.sourcegraph.com/app#optional-batch-changes-precise-code-intel)
      that, if you want the optional dependencies (`docker` and `src`) they must **"be installed and on your PATH"**.
    * The problem is that there is a "software installation/configuration misalignment" between traditional macOS desktop
      applications and mainstream developer tooling. [macOS apps by default launch with a PATH equal to `/usr/bin:/bin:/usr/sbin:/sbin`](https://apple.stackexchange.com/a/243946).
      By contrast, consider developer tools like Docker Desktop for Mac which installs the `docker` executable (by way of
      a symlink) to `/usr/local/bin/docker`. Consider HomeBrew-managed software which symlinks executables to multiple
      locations like `/opt/homebrew/bin`. To bridge this gap, I tried a few things that didn't work. Failed attempt #1:
      symlinking to `/usr/bin` which is locked down in recent versions of macOS. Failed attempt #2: changing the `Info.plist`
      to customize the PATH via the `LSEnvironment` property doesn't work because only the application developer who signed
      the app can change the `Info.plist` (my weak understanding). I wound up doing the `launchctl config user path` trick
      described in the earlier linked StackExchange answer.
5. Start it
    * It's neat that the Sourcegraph App is packaged as a proper desktop app. In the macOS Finder, you literally see the
      file `Sourcegraph App` and it has file kind equal to `Application`. It shows up in Finder in the `Applications`
      section which means it's installed in the directory `/Applications/`. It's ~800MB.
    * Open Spotlight (⌘ + Space), type `Sourcegraph App` and hit enter. It opens a little window showing `Stop`, `Start`,
      and `Show log` buttons. You access the web UI from your browser at `http://127.0.0.1:3080/`. It makes sense that it
      doesn't have to be an Electron app. I guess why bother, right?

My next step is to figure out indexing. Right now, indexing (is it precise code indexing??) isn't totally working. When
I navigate to the `http://127.0.0.1:3080/site-admin/repositories` page I see all the repos I expect to see and they all
have the `CLONED` status. When I click into the settings for any of them, and go to the `Indexing` page, I see "initial
indexing in progress". I think this is because I'm not running the optional `zoekt-indexserver` service which needs to
run in a Docker container and also the `src` CLI tool needs to exist. See the [(Optional) batch changes & precise code intel](https://docs.sourcegraph.com/app#optional-batch-changes-precise-code-intel)
section of the docs.


## Wish List

Getting Sourcegraph App up and running and with all the bells and whistles is a work-in-progress. Here are my TODOs.

* [x] DONE Install the current version of `src`, the Sourcegraph CLI tool. I already have an old installation. Get it up-to-date
  and make sure it works. This is one of the optional things needed for indexing.
* [x] DONE (I got Sourcegraph to find Docker by the `launchctl` trick) Likewise, run Docker at the same time as the Sourcegraph App. See if indexing works.
    * I'm running into an issue. For some reason I'm getting
    * > exec: \"docker\": executable file not found in $PATH"}}
    * Even though `docker` is at `/usr/local/bin/docker` and this dir is in `/etc/paths` so I would think the Sourcegraph
      process should have that on its path? It is a symlink though so maybe that's it. I'm going to reproduce this from a
      singe-file Go program.
    * UPDATE: This is the problem: https://apple.stackexchange.com/a/243946 . The PATH for apps launched from Finder/Spotlight
      don't have `/usr/local/bin` on the PATH.
* [ ] What's going on with the 'initial indexing in progress' message?
* [ ] Sync my personal repos. Should I sync from local or from GitHub integration?
* [ ] Consider building Sourcegraph "app" from source. Is it all in the OSS repo? I don't necessarily want to maintain
  a fork but I might want to patch some things (and maybe disable some things).


## Reference

* [Sourcegraph App docs](https://docs.sourcegraph.com/app)
