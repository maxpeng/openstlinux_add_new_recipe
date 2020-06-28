# OpenSTLinux - Creating a recipe for a git source tree

## Part 1 - Creating the recipe by devtool

This part describes how to use `devtool` to create a recipe from a source tree checked out from a git repository.

[cpputest_example] is an example demonstrating the usage of [Cpputest](http://cpputest.github.io/manual.html), it is hosted in github - https://github.com/maxpeng/cpputest_example and uses [CMake](https://cmake.org/) as the build system. I use it as an example to demonstrate how to use `devtool` to create a recipe for it.

This is the actual execution result of the executable, built by the recipe, runs on the [STM32MP157C-DK2 discovery board]:

```text
root@stm32mp1:~# cpputest_example_pythagorean
Hypotenuse of a right triangle with 2 sides as (30, 40) is 50.00.
```

### devtool

Before you are able to use `devtool`, you need to source the build environment setup script.

Using the `--help` option of `devtool` gives you a list of subcommands.

   ```text
   ~/stm32mp1 $ devtool --help
   NOTE: Starting bitbake server...
   usage: devtool [--basepath BASEPATH] [--bbpath BBPATH] [-d] [-q]
                  [--color COLOR] [-h]
                  <subcommand> ...
   
   OpenEmbedded development tool
   
   options:
     --basepath BASEPATH  Base directory of SDK / build directory
     --bbpath BBPATH      Explicitly specify the BBPATH, rather than getting it
                          from the metadata
     -d, --debug          Enable debug output
     -q, --quiet          Print only errors
     --color COLOR        Colorize output (where COLOR is auto, always, never)
     -h, --help           show this help message and exit
   
   subcommands:
     Beginning work on a recipe:
       add                  Add a new recipe
       modify               Modify the source for an existing recipe
       upgrade              Upgrade an existing recipe
     Getting information:
       status               Show workspace status
       search               Search available recipes
       latest-version       Report the latest version of an existing recipe
     Working on a recipe in the workspace:
       build                Build a recipe
       rename               Rename a recipe file in the workspace
       edit-recipe          Edit a recipe file
       find-recipe          Find a recipe file
       configure-help       Get help on configure script options
       update-recipe        Apply changes from external source tree to recipe
       reset                Remove a recipe from your workspace
       finish               Finish working on a recipe in your workspace
     Testing changes on target:
       deploy-target        Deploy recipe output files to live target machine
       undeploy-target      Undeploy recipe output files in live target machine
       build-image          Build image including workspace recipe packages
     Advanced:
       create-workspace     Set up workspace in an alternative location
       export               Export workspace into a tar archive
       import               Import exported tar archive into workspace
       extract              Extract the source for an existing recipe
       sync                 Synchronize the source tree for an existing recipe
   Use devtool <subcommand> --help to get help on a specific command
   ```

### Steps of creating the recipe by devtool

These are the steps of using `devtool` to create a recipe for [cpputest_example]:

1. Clone the source from github.

   ```text
   ~/stm32mp1 $ git clone https://github.com/maxpeng/cpputest_example.git
   ```

2. Create the workspace.

   ```text
   max@p50-2:~/work/st/stm32mp1 $ devtool create-workspace w1
   NOTE: Starting bitbake server...

   ~/stm32mp1 $ tree -L 1
   .
   ├── cpputest_example
   ├── openstlinux-20-02-19
   └── w1

3. Add a `cpputest-example` recipe from the `cpputest_example` source folder.

   To add a recipe from the given source source, use this short form of `devtool add` command:

   ```text
   devtool add recipename srctree

   Adds a new recipe to the workspace to build a specified source tree.

   arguments:
     recipename            Name for new recipe to add (just name - no version,
                           path or extension).
     srctree               Path to external source tree.
   ```

   This is the output of `add` sub-command to create a `cpputest-example` from the already checked out `cpputest_example` source tree:

   ```text
   ~/stm32mp1 $ devtool add cpputest-example ./cpputest_example/
   NOTE: Starting bitbake server...
   NOTE: Starting bitbake server...
   NOTE: Reconnecting to bitbake server...
   NOTE: Starting bitbake server...
   NOTE: Recipe /home/max/stm32mp1/w1/recipes/cpputest-example/cpputest-example_git.bb has been automatically created; further editing may be required to make it fully functional
   ```

   This is the workspace structure after we added the recipe using `devtool add` command:

   ```text
   ~/stm32mp1 $ tree w1
   w1
   ├── README
   ├── appends
   │   └── cpputest-example_git.bbappend
   ├── conf
   │   └── layer.conf
   └── recipes
       └── cpputest-example
           └── cpputest-example_git.bb
   ```

   Let's see what the recipe content is.

   ```text
   ~/stm32mp1 $ cat w1/recipes/cpputest-example/cpputest-example_git.bb

   # Recipe created by recipetool
   # This is the basis of a recipe and may need further editing in order to be fully functional.
   # (Feel free to remove these comments when editing.)
   
   # WARNING: the following LICENSE and LIC_FILES_CHKSUM values are best guesses - it is
   # your responsibility to verify that the values are complete and correct.
   LICENSE = "MIT"
   LIC_FILES_CHKSUM = "file://LICENSE;md5=074e244ec74f50203bb434d345e1db46"
   
   SRC_URI = "git://github.com/maxpeng/cpputest_example.git;protocol=https"
   
   # Modify these as desired
   PV = "1.0+git${SRCPV}"
   SRCREV = "82df4e6202df40b3267a5462a3414d2daa956853"
   
   S = "${WORKDIR}/git"
   
   inherit cmake
   
   # Specify any options you want to pass to cmake using EXTRA_OECMAKE:
   EXTRA_OECMAKE = ""
   ```

   From the content of generated recipe, we can see the `devtool add` command automatically determines that the source tree is using CMake and generate the recipe to inherit `cmake` class accordingly.

4. Build the target via the generated recipe.

   ```text
   ~/stm32mp1 $ devtool build cpputest-example
   NOTE: Starting bitbake server...
   NOTE: Started PRServer with DBfile: /home/max/stm32mp1/openstlinux-20-02-19/build-openstlinuxweston-stm32mp1/cache/prserv.sqlite3, IP: 127.0.0.1, PORT: 40987, PID: 8899
   Parsing recipes: 100% |##############################################################| Time: 0:01:25Parsing of 2531 .bb files complete (0 cached, 2531 parsed). 3579 targets, 83 skipped, 0 masked, 0 errors.
   NOTE: Resolving any missing task queue dependencies

   Build Configuration:
   BB_VERSION           = "1.40.0"
   BUILD_SYS            = "x86_64-linux"
   NATIVELSBSTRING      = "universal"
   TARGET_SYS           = "arm-ostl-linux-gnueabi"
   MACHINE              = "stm32mp1"
   DISTRO               = "openstlinux-weston"
   DISTRO_VERSION       = "2.6-snapshot-20200627"
   TUNE_FEATURES        = "arm armv7ve vfp thumb neon vfpv4 callconvention-hard cortexa7"
   TARGET_FPU           = "hard"
   DISTRO_CODENAME      = "thud"
   ACCEPT_EULA_stm32mp1 = "1"
   GCCVERSION           = "8.%"
   PREFERRED_PROVIDER_virtual/kernel = "linux-stm32mp"
   meta-python
   meta-oe
   meta-oe
   meta-gnome
   meta-xfce
   meta-initramfs
   meta-multimedia
   meta-networking
   meta-webserver
   meta-filesystems
   meta-perl
   meta-python          = "HEAD:436cf0aa2b2802da706588d4daa1a8240d172df8"
   meta-st-stm32mp      = "HEAD:8055ad11c92144e4a147a00834de53a2de21e42d"
   meta-qt5             = "HEAD:1520d5b2b2beec5e1c3209d3178219e93ef08bca"
   meta-st-openstlinux  = "HEAD:f023f39b13fa13f6391be5fbc10fb2bf7369402a"
   meta                 = "HEAD:cd7cf933b3235560ec71576d8f3836dff736a39f"
   w1                   = "<unknown>:<unknown>"
   
   Initialising tasks: 100% |###########################################################| Time: 0:00:01Sstate summary: Wanted 4 Found 4 Missed 0 Current 122 (100% match, 100% complete)
   NOTE: Executing SetScene Tasks
   NOTE: Executing RunQueue Tasks
   NOTE: cpputest-example: compiling from external source tree /home/max/stm32mp1/cpputest_example
   NOTE: Tasks Summary: Attempted 534 tasks of which 525 didn't need to be rerun and all succeeded.
   NOTE: Writing buildhistory
   ```

In the next part - [Part 2 - Using bitbake-layers to add the recipe to the build environment](add_the_layer_by_bitbake-layers.md), I will describe how to use `bitbake-layers` to create a new layer, populate the recipe to the new  layer, and finally add the new layer to the build environment.

## References

1. [STM32 MPU Wiki - OpenEmbedded - devtool](https://wiki.st.com/stm32mpu/wiki/OpenEmbedded_-_devtool)
2. [Yocto Project Application Development and the Extensible Software Development Kit (eSDK) - Using devtool in Your SDK Workflow](https://www.yoctoproject.org/docs/current/sdk-manual/sdk-manual.html#using-devtool-in-your-sdk-workflow)
3. [Yocto Project Reference Manual - devtool Quick Reference](https://www.yoctoproject.org/docs/3.1/ref-manual/ref-manual.html#ref-devtool-reference) 

[cpputest_example]: https://github.com/maxpeng/cpputest_example
[STM32MP157C-DK2 discovery board]: https://www.st.com/en/evaluation-tools/stm32mp157c-dk2.html
