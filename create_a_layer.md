# OpenSTLinux - Create a recipe for a git repository

## Abstract

1. Using `devtool` to create a recipe from the checkout source of a git repository.
2. Using `bitbake-layers` to create a layer for placing the recipe..

## Creating the recipe by devtool

[cpputest_example](https://github.com/maxpeng/cpputest_example) is an example demonstrating the usage of [Cpputest](http://cpputest.github.io/manual.html), the used build system is [CMake](https://cmake.org/).

These are the steps of using `devtool` to create a recipe for it:

1. clone the source.

   ```text
   ~/stm32mp1 $ git clone https://github.com/maxpeng/cpputest_example.git
   ```

2. create the workspace.

   ```text
   max@p50-2:~/work/st/stm32mp1 $ devtool create-workspace w1
   NOTE: Starting bitbake server...

   ~/stm32mp1 $ tree -L 1
   .
   ├── cpputest_example
   ├── openstlinux-20-02-19
   └── w1

3. add a `cpputest-example` recipe from the `cpputest_example` source folder by  this command:

   ```text
   devtool add recipename srctree
   ```

   where `recipename` is the name for new recipe to add, ` srctree` is the path to external source tree.

   This is the actual output of the command:

   ```text
   ~/stm32mp1 $ devtool add cpputest-example ./cpputest_example/
   NOTE: Starting bitbake server...
   NOTE: Starting bitbake server...
   NOTE: Reconnecting to bitbake server...
   NOTE: Starting bitbake server...
   NOTE: Recipe /home/max/stm32mp1/w1/recipes/cpputest-example/cpputest-example_git.bb has been automatically created; further editing may be required to make it fully functional
   ```

   This is the workspace structure after we added the recipe.

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

4. make sure we can build the target specified by the recipe.

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

## Using bitbake-layers

Yocto/OpenEmbedded provides a BitBake layer management tool called `bitbake-layers`. Using the `--help` option gives you a list of subcommands.

```text
$ bitbake-layers --help
NOTE: Starting bitbake server...
usage: bitbake-layers [-d] [-q] [-F] [--color COLOR] [-h] <subcommand> ...

BitBake layers utility

optional arguments:
  -d, --debug           Enable debug output
  -q, --quiet           Print only errors
  -F, --force           Force add without recipe parse verification
  --color COLOR         Colorize output (where COLOR is auto, always, never)
  -h, --help            show this help message and exit

subcommands:
  <subcommand>
    add-layer           Add one or more layers to bblayers.conf.
    remove-layer        Remove one or more layers from bblayers.conf.
    flatten             flatten layer configuration into a separate output
                        directory.
    show-layers         show current configured layers.
    show-overlayed      list overlayed recipes (where the same recipe exists
                        in another layer)
    show-recipes        list available recipes, showing the layer they are
                        provided by
    show-appends        list bbappend files and recipe files they apply to
    show-cross-depends  Show dependencies between recipes that cross layer
                        boundaries.
    layerindex-fetch    Fetches a layer from a layer index along with its
                        dependent layers, and adds them to conf/bblayers.conf.
    layerindex-show-depends
                        Find layer dependencies from layer index.
    create-layer        Create a basic layer

Use bitbake-layers <subcommand> --help to get help on a specific command
```


## References

1. [How to create a new open embedded layer - stm32mpu]
2. [Yocto Project Development Tasks Manual - Managing Layers](https://www.yoctoproject.org/docs/latest/dev-manual/dev-manual.html#managing-layers)

[How to create a new open embedded layer - stm32mpu]: https://wiki.st.com/stm32mpu/wiki/How_to_create_a_new_open_embedded_layer