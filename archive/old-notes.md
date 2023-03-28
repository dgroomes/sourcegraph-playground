# Old Notes

Everything below the dividing line is a literal copy/paste of my old notes when I tried to run Sourcegraph locally in 2022.
This content is captured for posterity. So, this information is **OUT OF DATE**.

---

UPDATE 2022-07-16: I'm leaving this in a messy state. When I first started using SourceGraph, I was able to do it without
Docker containers. But I revisited it again and now I'm having trouble I'll just leave my many notes below. I hope that
SourceGraph can once again run on macOS without Docker (or if Docker somehow solves all its file system etc. performance
problems). I like the product. Fortunately because all my repos are public on GitHub, Sourcegraph should be able to continue
indexing them. (UPDATE some time later: Sourcegraph stopped supporting sync for personal repos and only a smattering of
my repos remained indexed, so I eventually stopped using it and did normal `grep/rg` and then briefly used GitHub's new
code search).

TODO: try with only one zoekt instance. Def continue to use the Docker-based ctags because it is segfaulting using the macOS one.
Double check killed processes when done. Can we get to low CPU usage? I swear we had it at one point.

EXPERIMENTAL (answer: yes `sg run`): Can we try running instances individually?

* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run oss-frontend`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run oss-web`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run caddy`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run gitserver`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run oss-repo-updater`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run zoekt-index-0`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run zoekt-index-1`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run zoekt-web-0`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run zoekt-web-1`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run oss-worker`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run oss-symbols`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run searcher`
* `OFFLINE=true sg --verbose --skip-auto-update --disable-analytics run syntax-highlighter`

## Instructions

Follow these instructions to run a local Sourcegraph cluster. These instructions follow the [Sourcegraph *Getting Started*](https://github.com/sourcegraph/sourcegraph/tree/main/doc/dev/getting-started)
guide and involve actually building Sourcegraph from source. By contrast, you can follow [the "full Docker approach"](https://about.sourcegraph.com/#get-started)
and run Sourcegraph in a pre-built Docker image but my Mac struggled in performance with that approach. So, I opted for
the "long route".

1. Open Sourcegraph's [*Local Environment Setup*](https://github.com/sourcegraph/sourcegraph/blob/80a3ece6ade6127544a83c09dd2918b327f9a269/doc/dev/setup/index.md) page
    * This is a starting point. Your journey will span across several other documentation pages within the Sourcegraph
      monorepo. There's a lot of content there! I've copied and rephrased some of the instructions in the steps that follow.
      I have not comprehensively repeated the instructions, so you will still have to follow the Sourcegraph docs.
2. Clone Sourcegraph:
    * ```shell
      git clone https://github.com/sourcegraph/sourcegraph.git
      ```
    * ```shell
      cd sourcegraph
      ```
3. Build `sg` from source
    * Make sure you are in the root of the Sourcegraph repository, then run the command that follows.
    * ```shell
      go build -o /usr/local/bin/sg ./dev/sg
      ```
    * The `sg` tool is a comprehensive developer tool for working with Sourcegraph. It's pretty cool. Without it, you
      would need to execute a lot more shell scripts and suffer the user experience of error messaging and un-styled log
      output that's normal with shell scripts. The `sg` tool, by contrast, logs with a high signal-to-noise ratio.
4. Build the syntax-server
    * We do not want to run with Rosetta emulation (Intel) and always want to use Apple Silicon. The Docker images used
      as part of the Sourcegraph system all target amd64 (Intel). We need to build them locally so they target ARM.
    * Navigate to the `syntax-server` directory and build the image with the following commands.
    * ```shell
      cd docker-images/syntax-highlighter
      IMAGE=syntax-highligher:local ./build.sh
      ```
6. Override the configuration
    * Create a `sg.config.overwrite.yaml` file in the root of the Sourcegraph repository. It is `.gitingore`-ed so you
      don't have to worry about accidentally committing it. This file overrides the default configuration. It's necessary
      because we stray from the default installation conventions and instead do things like run Postgres manually instead
      of using a Homebrew service. Use the below contents:
    * ```yaml
      env:
        PGUSER: postgres
        PGPASSWORD: ""
        PGDATABASE: postgres
      ```
7. Start Postgres
    * I like to use my own Bash functions to do this. See the [`pgStart` function](https://github.com/dgroomes/my-config/blob/d3acf6628910177ff749a321e7a3ad88c84e4ec2/bash/bash-functions.sh#L69).
8. Start Redis
    * ```shell
      brew services start redis
      ```
9. Override tool versions configuration
    * The `sg` tool will enforce that certain versions of commandline tools are installed. I was having trouble with Cargo.
      I had a more recent version installed than Sourcegraph expected. So, I modified the file `.tool-versions` to specify
      the version of Cargo I had installed.
10. Setup Node
    * Make sure Node Version Manager (nvm) is installed: <https://github.com/nvm-sh/nvm>
    * Install the exact version of Node specified in the `.tool-versions` file with the following command:
    * ```shell
      nvm install 16.7.0
      ```
11. Install `yarn` (the legacy version; the one that everyone is using)
    * ```shell
      npm install --location=global yarn
      ```
12. Configure the fake host name
    * Add the following to `/etc/hosts`
      ```
      127.0.0.1        localhost sourcegraph.test
      ```
    * This is really cool. This step plus an HTTP reverse proxy let's us use HTTPS locally (but I don't really get it)!
13. Run the `sg` setup process
    * ```shell
      sg setup --oss
      ```
    * This will guide you through the setup process. It will show you quite a few commandline programs that need to be installed
      and updated. Tend to prefer the `I want to fix these manually` option.

[//]: # (10. Initialize Postgres)
[//]: # (    * ```shell)
[//]: # (      psql postgres --command "create role sourcegraph with login superuser")
[//]: # (      psql postgres --command "alter user sourcegraph with password 'sourcegraph'")
[//]: # (      psql postgres --command "create database sourcegraph with owner=sourcegraph encoding=UTF8")
[//]: # (      ```)
[//]: # (12. Start the reverse proxy)

[//]: # (    * Note: I don't think you need to execute this command after the first time, but I'm not sure.)

[//]: # (    * `./dev/caddy.sh reverse-proxy --to localhost:3080`)

[//]: # (    * Note: you will be interacting with Sourcegraph in the browser at the non-HTTPS address <http://localhost:3080>. The)

[//]: # (      HTTPS stuff is still happening though, it's just under-the-hood. If you want to disable HTTPS, you will be met with)

[//]: # (      an annoying warning banner that's constantly displayed in the top inch of the Sourcegraph UI. So don't skip the)

[//]: # (      HTTPS stuff.)
13. Start Sourcegraph
    * ```shell
      OFFLINE=true sg --skip-auto-update --disable-analytics start oss 
      ```
    * Note: it's necessary to use the open source version of Sourcegraph (the `oss` argument).
    * Note: in an attempt to maximize reliability and predictability, I've used the `--skip-auto-update` and
      `--disable-analytics` flags. I also tried `OFFLINE=true` but when I use that I can't get the "Connect code hosts"
      page to load all the way. It stays blank where instead it should at least show an error message.
14. Install the `src` CLI tool
    * Follow instructions at <https://github.com/sourcegraph/src-cli>.
15. Serve your git repos:
    * ```shell
      src serve-git <repos-directory>
      ```
      And set the parameterized argument to the directory you
      keep your git repositories on your computer. For me, that's `~/repos/personal`.
16. Connect the *code host*
    * Connect Sourcegraph to the `serve-git` process so that Sourcegraph can find the git repos.
    * Go to <http://localhost:3080/site-admin/external-services/new?id=srcservegit>
    * In the editor in the web page, change the value of the "url" field to <http://[::]:3434>.


Alternatively, just start Sourcegraph in a single Docker container (this will consume half your Mac's compute resources):

```shell
docker run -d --publish 7080:7080 --publish 127.0.0.1:3370:3370 --rm --volume ~/.sourcegraph/config:/etc/sourcegraph --volume ~/.sourcegraph/data:/var/opt/sourcegraph sourcegraph/server:3.41.0
```

## Reference

* [Getting started](https://docs.sourcegraph.com/getting-started)
