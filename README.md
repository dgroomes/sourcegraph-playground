# sourcegraph-playground

📚 Learning and exploring Sourcegraph.

> Sourcegraph makes it easy to read, write, and fix code—even in big, complex codebases.
>
> --<cite>https://about.sourcegraph.com</cite>


## Overview

This repository is my personal playground for learning and exploring Sourcegraph. In it, I have notes, commands and
references about Sourcegraph products.

My main interest in Sourcegraph is using it to search my own fleet of many small GitHub repositories. I'm especially sold
on [precise code navigation](https://docs.sourcegraph.com/code_navigation/explanations/precise_code_navigation). I've
also used Sourcegraph to wade through large open source projects and I'm optimistic about making use of features like
batch changes, automated integrations to the Sourcegraph APIs (GraphQL + REST), the Sourcegraph browser extension, and
now the AI-coding assistant named [Cody](https://about.sourcegraph.com/cody).

---
**Large Language Models entered the chat...**

I have to admit... I undersold the last item in that sentence: Cody.

Operationalized "AI power" (read: LLMs) has dwarfed the value proposition of all other software tools/languages/frameworks
that I had on my todo-list for learning. [It is a volcano 🌋](https://about.sourcegraph.com/blog/cheating-is-all-you-need).

I need to figure out how to use AI tools like Cody and the rest of them. In broad
strokes, I need to spend my effort learning how to:

* interact with AI
* instruct AI
* think about AI's capabilities/limitations
* map/reduce domain problems into small (8KB, 32KB) puzzles that can be solved by AI

And it's already been fun and fruitful to use these tools and become enthusiastic about what I know they can do.

---

My experience with Sourcegraph is described by an "Awareness, Trial, Productivity, (Repeat)" arc below.


### Awareness

I heard about Sourcegraph for the first time from advertisements on the [Changelog family of podcasts](https://changelog.com/podcast)
around 2020 (maybe earlier or later?).


### Trial

I got up and running with a local instance of Sourcegraph using the official Docker containers in October 2021. While a
success, running via containers was too intensive/slow on my Mac for me to have it up and running all the time. 

I got up and running with the "build from source" route. I can't really remember why I didn't use this approach in the
long term. Partially because of ctags problems (hard to remember)? I think the main reason was that it was just
so easy/effective to use Sourcegraph.com directly because it could index my personal code.


### Productivity

> my own data + a good search UX = good

My usage of Sourcegraph remained pretty basic—but basics are the best 👑. I would search over my own personal (public)
GitHub repositories to find snippets of code or non-code information like my extensive notes across my many small repos.
I did this between November 2021 up until personal repo indexing (through the program named *personal code beta*) was
discontinued in August 2022.


### Trial Part 2: Failure to launch

After personal repo indexing was discontinued, I wanted an alternative. I'm willing to pay for something, but
Sourcegraph only sold "a single-tenant, managed version of Sourcegraph" for teams (makes sense). So, the other
option was to run Sourcegraph myself. I came back to this `sourcegraph-playground` repository to re-visit my notes
and get up and running with Sourcegraph locally but I couldn't get it to work. Sourcegraph had grown more sophisticated
since I was first able to build and run it. With sophistication comes complexity. I couldn't tackle the complexity. See 
my extensive notes in the file [OLD-NOTES.md](OLD-NOTES.md).


### Trial Part 3: "Sourcegraph App"

It's March 2023 and Sourcegraph launched a desktop app that runs a local version of Sourcegraph from a single binary.
This is perfect! See the announcement: [*Announcing the Sourcegraph app*](https://about.sourcegraph.com/blog/announcing-sourcegraph-app).

I installed it and it's promising although I'm having issues with indexing my repos. My next steps are to capture the
issues in detail, and make it clear how to re-produce them. Even if I'm the only audience, that's fine. The Sourcegraph
team is very generous in [the Sourcegraph Discord](https://discord.com/invite/DZtdAxTfrM).


## Instructions: Running Sourcegraph App (Desktop app)

These are my instructions for running *Sourcegraph App* (the desktop app) on my personal computer. I'm running macOS
Ventura (13.2.1) on an Apple Silicon Macbook.

1. Install Sourcegraph App
   * I installed via the `.dmg` installer
2. Start it
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


## Reference

* [Sourcegraph + AI: A Steve Yegge blog post (including rant)](https://about.sourcegraph.com/blog/cheating-is-all-you-need).
  * > There is something **legendary and historic** happening in software engineering, right now as we speak, and yet most
      of you don’t realize at all how big it is.
  * > The ultra-rare trillion-dollar money volcano
  * > In one shot, ChatGPT has produced completely working code from a sloppy English description! With voice input wired
      up, I could have written this program by asking my computer to do it.
  * > Cody is Sourcegraph’s new LLM-backed coding assistant.
* [Sourcegraph Discord](https://discord.com/invite/DZtdAxTfrM)
* [Sourcegraph App docs](https://docs.sourcegraph.com/app)
