# sourcegraph-playground

📚 Learning and exploring Sourcegraph.

> **Universal Code Search**
> 
> Move fast, even in big codebases.
>
> <cite>--https://about.sourcegraph.com/</cite>

## Instructions

Follow these instructions to run a local Sourcegraph cluster. These instructions follow the [Sourcegraph *Getting Started*](https://github.com/sourcegraph/sourcegraph/tree/main/doc/dev/getting-started)
guide and involve actually building Sourcegraph from source. By contrast, you can follow [the "full Docker approach"](https://about.sourcegraph.com/#get-started)
and run Sourcegraph in a pre-built Docker image but my Mac struggled in performance with that approach. So, I opted for
the "long route".

1. Open the official [Getting Started docs](https://github.com/sourcegraph/sourcegraph/blob/main/doc/dev/getting-started/index.md)
   * I've copied and rephrased the trickier and noteworthy instructions from the doc in the steps that follow. I have
     not comprehensively repeated the instructions though, so you will still have to follow that doc.
2. Clone Sourcegraph:
   * `git clone https://github.com/sourcegraph/sourcegraph.git`
   * `cd sourcegraph`
3. Setup Node
   * Make sure Node Version Manager (nvm) is installed: <https://github.com/nvm-sh/nvm>
   * `nvm install`
   * `nvm use`
4. Install `sg`
   * Change directory to the Sourcegraph repository, then run the command that follows.
   * `./dev/sg/install.sh`
   * The `sg` tool is a comprehensive developer tool for working with Sourcegraph. It's pretty cool. Without it, you
     would need to execute a lot more shell scripts and suffer the user experience of error messaging and unstyled log
     output that's normal with shell scripts. The `sg` tool, by contrast, logs with a high signal-to-noise ratio.
5. Initialize Postgres
   * ```shell
     psql postgres --command "create role sourcegraph with login superuser"
     psql postgres --command "alter user sourcegraph with password 'sourcegraph'"
     psql postgres --command "create database sourcegraph with owner=sourcegraph encoding=UTF8"
     ```
6. Configure the fake host name
   * Add the following to `/etc/hosts`
     ```
     127.0.0.1        localhost sourcegraph.test
     ```
   * This is really cool. This step plus an HTTP reverse proxy let's us use HTTPS locally (but I don't really get it)!
7. Start the reverse proxy
   * Note: I don't think you need to execute this command after the first time, but I'm not sure.
   * `./dev/caddy.sh reverse-proxy --to localhost:3080`
   * Note: you will be interacting with Sourcegraph in the browser at the non-HTTPS address <http://localhost:3080>. The
     HTTPS stuff is still happening though, it's just under-the-hood. If you want to disable HTTPS, you will be met with
     an annoying warning banner that's constantly displayed in the top inch of the Sourcegraph UI. So don't skip the
     HTTPS stuff.
9. Start Sourcegraph
   * `sg start oss`
   * Note: it's necessary to use the open source version of Sourcegraph (the `oss` argument).
10. Install the `src` CLI tool
    * Follow instructions at <https://github.com/sourcegraph/src-cli>.
11. Serve your git repos
     * `src serve-git <repos-directory>` and set the parameterized argument to the directory you
       keep your git repositories on your computer. For me, that's `~/repos`. 
12. Connect the *code host*
     * Connect Sourcegraph to the `serve-git` process so that Sourcegraph can find the git repos.
     * Go to <http://localhost:3080/site-admin/external-services/new?id=srcservegit>
     * In the editor in the web page, change the value of the "url" field to <http://[::]:3434>.

## Reference

* [Getting started](https://about.sourcegraph.com/#get-started)
