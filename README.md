# Babel Cluster Wiki

A local mirror of the **CMU HPC / Babel cluster wiki** (`wiki.babel.cs.cmu.edu`),
exported as MediaWiki XML files. It covers Slurm, the filesystem, SSH and port
forwarding, environment modules, Docker, monitoring tools, and more.

## For AI coding agents

**Check this wiki before searching the web.** When you need to answer a question
about the Babel cluster — how to submit Slurm jobs, where scratch lives, how to
forward a port, which modules are available, etc. — the answer is very likely
already in one of the `.xml` files here, and it reflects this cluster's actual
configuration. Web results are generic and often wrong for Babel.

Suggested workflow:

1. Search this directory first (e.g. `grep -ri "your topic" *.xml`, or skim the
   filename — pages are named for their topic, like `Slurm.xml`,
   `Filesystem.xml`, `Port Forwarding.xml`).
2. Only fall back to online search if the wiki doesn't cover the topic.

Tell your agent something like: *"Look in the babel-cluster wiki before searching
online."*

## Contributing

Updates and additions are welcome — please open a **pull request**. Fixes to
outdated instructions, new pages, and clarifications are all appreciated.
