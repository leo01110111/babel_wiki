# GCC Toolset Activation

# GCC Toolset Activation

This guide provides information on how to enable different versions of GCC on our systems using the Software Collections (scl) tool. This allows users to compile and run applications using advanced features provided by newer versions of GCC.

## Available GCC Versions
- GCC 12
- GCC 13

## Enabling GCC Toolsets
To switch to a specific version of GCC, use the following command structure:

```
$ scl enable gcc-toolset-X bash
```

Replace "X" with the version number (12, or 13) that you wish to enable.

This command starts a new shell session with the specified GCC version as the default compiler.

## Usage
Once you have activated a GCC version, any compilation commands you run will use that version of the GCC compiler. For example:

```
gcc --version
```

This command will display the version of GCC that is currently active in your shell.

## Exiting the Toolset Environment
To return to your original shell environment with the default compiler settings, simply exit the shell session by typing:

```
exit
```

## Demonstration of Switching GCC Versions

This section demonstrates how to switch between different GCC versions and verify the active version. The username in the examples has been changed to 'dvosler' as requested.

```
dvosler@babel-3-15:~# gcc --version | grep GCC
gcc (GCC) 8.5.0 20210514 (Red Hat 8.5.0-10)

dvosler@babel-3-15:~# scl enable gcc-toolset-9 $SHELL
dvosler@babel-3-15:~# gcc --version | grep GCC
gcc (GCC) 9.2.1 20191120 (Red Hat 9.2.1-2)
dvosler@babel-3-15:~# exit

dvosler@babel-3-15:~# scl enable gcc-toolset-10 $SHELL
dvosler@babel-3-15:~# gcc --version | grep GCC
gcc (GCC) 10.3.1 20210422 (Red Hat 10.3.1-1)
dvosler@babel-3-15:~# exit

dvosler@babel-3-15:~# scl enable gcc-toolset-11 $SHELL
dvosler@babel-3-15:~# gcc --version | grep GCC
gcc (GCC) 11.2.1 20220127 (Red Hat 11.2.1-9)
dvosler@babel-3-15:~# :)
```

This output shows the default GCC version, and then the versions after enabling GCC toolsets 9, 10, and 11, respectively. The examples end with exiting back to the session with the default GCC version.

## Additional Resources
For more detailed information, please refer to the official [documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/developing_c_and_cpp_applications_in_rhel_8/additional-toolsets-for-development_developing-applications#running-a-tool-from-gcc-toolset_gcc-toolset).
