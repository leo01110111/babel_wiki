# Environment Modules

[Category:Babel](Category%3ABabel.md)
[Category:Clusters](Category%3AClusters.md)
[Category:LTI](Category%3ALTI.md)

## How to Use Environment Modules in Linux

### Overview
Environment modules in Linux provide a convenient way to manage software environments by dynamically modifying the user's environment to make different software packages and versions available. This guide outlines the basic steps for using environment modules effectively.

### Checking Available Modules
To view the available modules on your system, use the following command:

```
module avail
```

### Loading a Module
To load a specific module into your environment, use the following syntax:

```
module load <module_name>
```

For example, to load the CUDA module:

```
module load cuda-12.3
```

### Verifying Loaded Modules
To confirm that a module has been successfully loaded, use the following command:

```
module list
```

This will display a list of all currently loaded modules.

### Using Software with Loaded Modules
Once a module is loaded, you can use the associated software as usual. For example, if you've loaded the CUDA module, you can compile CUDA programs or run CUDA-enabled applications.

### Unloading Modules
If a module is no longer needed, you can unload it using the following syntax:

```
module unload <module_name>
```

For example, to unload the CUDA module:

```
module unload cuda-12.3
```

### Permanent Configuration
To automate the loading of frequently used modules, you can add the relevant `module load` commands to your shell configuration file (e.g., .bashrc).

### Example

```
module load cuda-12.3
```

This loads the CUDA module version 12.3 into the environment.

### Writing Module Files
You can create custom module files to define your own environment modules, which can then be loaded using the `module load` command. Follow these steps to create a module file:

1. **Modules Directory:** Create the following dir `$HOME/privatemodules`

2. **Create the Module File:** Use a text editor to create a file with a descriptive name. For example, to create a module *cuda version 12.3*, create a file named `cuda-12.3`.

3. **Define the Module**: Here's an example for a CUDA 12.3 module:

```
#%Module
##
## cuda modulefile
##
## modulefiles/
##

set ver 12.3

set msg "This module adds CUDA $ver to various paths\n"

proc ModulesHelp { } {
        puts stderr $msg
}

module-whatis "Use CUDA tools $ver"

setenv CUDA_PATH /usr/local/$ver

prepend-path PATH /usr/local/cuda-${ver}/bin
prepend-path LD_LIBRARY_PATH /usr/local/cuda-${ver}/lib64
```

Adjust the paths and version numbers according to your specific setup. The `ModulesHelp` procedure provides a description of the module's purpose, which can be viewed using `module help <module_name>`.

4. **Load your Module**: Use `module load use.own` to load modules from your `$HOME/privatemodules` directory.

5. **View your Modules**: Use `module avail` to load modules from your `$HOME/privatemodules` directory.

```
(base) [dvosler@babel-4-7 ~]$ nvcc -V
-bash: nvcc: command not found

(base) [dvosler@babel-4-7 ~]$ module avail
--------------------------------- /usr/share/Modules/modulefiles ---------------------------------
cuda-11.6  cuda-11.7  cuda-11.8  cuda-12.0  cuda-12.1  cuda-12.2  cuda-12.3
dot  gcc-7.4  module-git  module-info  modules  null  use.own  zsh-5.9
------------------------------------- /usr/share/modulefiles -------------------------------------
mpi/mpich-x86_64  pmi/pmix-x86_64

(base) [dvosler@babel-4-7 ~]$ module load use.own
(base) [dvosler@babel-4-7 ~]$ module avail
--------------------------- /usr/share/Modules/modulefiles ----------------------- 
cuda-11.6  cuda-11.7  cuda-11.8  cuda-12.0  cuda-12.1  cuda-12.2  cuda-12.3
dot  gcc-7.4  module-git  module-info  modules  null  use.own  zsh-5.9
--------------------------- /usr/share/modulefiles -------------------------------
mpi/mpich-x86_64  pmi/pmix-x86_64
--------------------------- $HOME/privatemodules ---------------------------------
e-acc-99
```

6. **Verify the Module:** After loading the module, use `module list` to verify that it has been successfully loaded. You should see your custom module listed among the loaded modules.

7. **Testing:** Test your module to ensure that it sets up the environment correctly and provides access to the desired software.

### Conclusion
By following these steps, you can effectively manage software environments using environment modules in Linux.

This guide provides a comprehensive overview of using environment modules in Linux, suitable for both beginners and advanced users.
