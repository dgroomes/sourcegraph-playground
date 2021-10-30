# sourcegraph-playground

📚 Learning and exploring Sourcegraph.

> **Universal Code Search**
> 
> Move fast, even in big codebases.
>
> <cite>--https://about.sourcegraph.com/</cite>

## Instructions

Follow these instructions to run a local Sourcegraph cluster.

Caveat: These instructions are catered to me and my own needs.

1. Configure the location of your Git repositories
   * Edit the `docker-compose.yml` file to point to the directory on your computer that contains the Git repos you want
     Sourcegraph to search over. For me, that directory is `~/repos`. Change it to your needs.
1. Start Sourcegraph
   * `docker-compose up`
1. Configure Sourcegraph
   * Open the Sourcegraph UI at <http://127.0.0.1:7080/> and follow the instructions.
   * There are a number of steps, including waiting four Sourcegraph to clone and index all your Git repo.
* Code search!
   * Start searching in the UI! 

## Reference

* [Getting started](https://about.sourcegraph.com/#get-started)
