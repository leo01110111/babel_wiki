# Direnv

[Category:Software Development](Category%3ASoftware%20Development.md)
[Category:Tips](Category%3ATips.md)
[Category:Tools](Category%3ATools.md)

`direnv` is a program that acts like a plugin for your shell. Specifically, "it augments existing shells with a new feature that can load and unload environment variables depending on the current directory." (https://direnv.net/)
Beyond environment variables `direnv` is useful for automatically loading and unloading any environment that is associated with a particular directory/project.
Please see <https://direnv.net/> for basic setup and usage of `direnv`; it is already installed on `babel` servers, so that step can be skipped in setting it up.
**NB: Do not create or allow `.envrc` files in shared directories; this can cause problem for you and/or other people.**

## Activating conda environments
The primary use case for `direnv` on `babel` is easy conda environment activation without the need to activate a conda environment in your `.bashrc` file, as this can cause your shell to hang when you SSH into the cluster.
Instead, you can have `direnv` activate your conda environment for you when you enter into the directory of the relevant project.
This doesn't make conda any faster, but it prevents your session from hanging on login.
Also, it saves you the trouble of having to manually activate and deactivate your conda environments.

As conda environment can be activated by adding the following to `~/my_project/.envrc` (for example):

```
 eval "$(conda shell.bash activate my_environment)"
```

Make sure to `direnv allow` to permit this `.envrc` to take effect.
If you no longer need conda to activate immediately at every login, you can execute:

```
 conda init --reverse
```

This will remove the conda-activating code from your `~/.bashrc` which can cause the login into hang.
If your SLURM jobs are no longer able to find your conda environent (i.e., because your `~/.bashrc` no longer does any conda stuff), you can run the above `eval ...` in your scripts.
If you are only running a Python program, you can also just run `~/miniconda3/env/my_environment/bin/conda` without activating anything (assuming that is where your conda enviornments are).
