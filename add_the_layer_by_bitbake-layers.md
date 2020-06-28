# OpenSTLinux - Creating a recipe for a git source tree

## Part 2 - Using bitbake-layers to add the recipe to the build environment

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

1. Create `meta-example` layer directory.

   ```text
   ~/stm32mp1 $ bitbake-layers create-layer meta-example
   NOTE: Starting bitbake server...
   Add your new layer with 'bitbake-layers add-layer meta-example'
   ```

   Let's see the structure of the `meta-example` created by `bitbake-layers create-layer` command.

   ```text
   ~/stm32mp1 $ tree meta-example/
   meta-example/
   ├── COPYING.MIT
   ├── README
   ├── conf
   │   └── layer.conf
   └── recipes-example
       └── example
           └── example_0.1.bb
   ```

2. Finish the `cpputest-example` recipe created by `devtool add` command, and move it to the `meta-example` layer.

   ```text
   ~/stm32mp1 $ devtool finish cpputest-example meta-example/recipes-example
   NOTE: Starting bitbake server...
   NOTE: Started PRServer with DBfile: /home/max/stm32mp1/openstlinux-20-02-19/build-openstlinuxweston-stm32mp1/cache/prserv.sqlite3, IP: 127.0.0.1, PORT: 36131, PID: 11473
   Parsing recipes: 100% |##############################################################| Time: 0:01:27Parsing of 2532 .bb files complete (0 cached, 2532 parsed). 3580 targets, 83 skipped, 0 masked, 0 errors.
   NOTE: Updating SRCREV in recipe cpputest-example_git.bb
   NOTE: Moving recipe file to /home/max/stm32mp1/meta-example/recipes-example/cpputest-exampleNOTE: Leaving source tree /home/max/stm32mp1/cpputest_example as-is; if you no longer need it then please delete it manually
   ```

   After executes `devtool finish` command, we can see the `cpputest-example` recipe has been removed from the workspace `w1`.

   ```text
   max@p50-2:~/stm32mp1 $ tree w1
   w1
   ├── README
   ├── appends
   ├── conf
   │   └── layer.conf
   └── recipes
   ```
  
   Let's check the `meta-example` layer structure. We can see the `cpputest-example` has been moved to `meta-example/recipes-example/cpputest-example` directory.

   ```text
   ~/stm32mp1 $ tree meta-example
   meta-example
   ├── COPYING.MIT
   ├── README
   ├── conf
   │   └── layer.conf
   └── recipes-example
       ├── cpputest-example
       │   └── cpputest-example_git.bb
       └── example
           └── example_0.1.bb
   ```

   We delete the `example` recipe, since we don't use it.

   ```text
   meta-example
   ├── COPYING.MIT
   ├── README
   ├── conf
   │   └── layer.conf
   └── recipes-example
       └── cpputest-example
           └── cpputest-example_git.bb
   ```

3. Add the `meta-example` layer to `conf/bblayers.conf` of current build environment.

   At this moment, we have completed the work of `meta-example` layer to include
   `cpputest-example` recipe. It is the time to use `bitbake-layers add-layer` command
   to add the layer to `conf/bblayers.conf` of the build environment.

   Note: to add the layer to `conf/bblayers.conf` of the build environment, you must execute `bitbake-layers add-layer` from the build directory of current build environment, which is `~/stm32mp1/openstlinux-20-02-19/build-openstlinuxweston-stm32mp1` in our case.

   ```text
   ~/stm32mp1 $ cd openstlinux-20-02-19/build-openstlinuxweston-stm32mp1/
   ~/stm32mp1/openstlinux-20-02-19/build-openstlinuxweston-stm32mp1 $ bitbake-layers add-layer ../../meta-example
   NOTE: Starting bitbake server...
   ```

   To check `meta-example` layer is added to the build environment, use `bitbake-layers show-layers` command:

   ```text
   ~/stm32mp1/openstlinux-20-02-19/build-openstlinuxweston-stm32mp1 $ bitbake-layers show-layersNOTE: Starting bitbake server...
   layer                 path                                      priority
   ==========================================================================
   meta-python           /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-python  7
   meta-oe               /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-oe  6
   meta-oe               /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-oe  6
   meta-gnome            /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-gnome  7
   meta-xfce             /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-xfce  7
   meta-initramfs        /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-initramfs
   8
   meta-multimedia       /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-multimedia  6
   meta-networking       /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-networking  5
   meta-webserver        /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-webserver
   6
   meta-filesystems      /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-filesystems
     6
   meta-perl             /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-perl  6
   meta-python           /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-python  7
   meta-st-stm32mp       /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-st/meta-st-stm32mp  6
   meta-qt5              /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-qt5  7
   meta-st-openstlinux   /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-st/meta-st-openstlinux  5
   meta                  /home/max/stm32mp1/openstlinux-20-02-19/layers/openembedded-core/meta  5
   meta-custom           /home/max/stm32mp1/meta-custom    7
   w1                    /home/max/stm32mp1/w1             99
   meta-example          /home/max/stm32mp1/meta-example   6
   ```

4. Finally we can remove workspace layer (i.e. `w1` in our case) from current build environment.

   To remove the `w1` layer from current build environment, execute `bitake-layers remove-layer` command from build directory of current build environment.

   ```text
   max@p50-2:~/stm32mp1 $ cd openstlinux-20-02-19/build-openstlinuxweston-stm32mp1/
   max@p50-2:~/stm32mp1/openstlinux-20-02-19/build-openstlinuxweston-stm32mp1 $ bitbake-layers remove-layer w1
   NOTE: Starting bitbake server...
   max@p50-2:~/stm32mp1/openstlinux-20-02-19/build-openstlinuxweston-stm32mp1 $ bitbake-layers show-layersNOTE: Starting bitbake server...
   layer                 path                                      priority
   ==========================================================================
   meta-python           /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-python  7
   meta-oe               /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-oe  6
   meta-oe               /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-oe  6
   meta-gnome            /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-gnome  7
   meta-xfce             /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-xfce  7
   meta-initramfs        /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-initramfs
   8
   meta-multimedia       /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-multimedia  6
   meta-networking       /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-networking  5
   meta-webserver        /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-webserver
   6
   meta-filesystems      /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-filesystems
     6
   meta-perl             /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-perl  6
   meta-python           /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-openembedded/meta-python  7
   meta-st-stm32mp       /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-st/meta-st-stm32mp  6
   meta-qt5              /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-qt5  7
   meta-st-openstlinux   /home/max/stm32mp1/openstlinux-20-02-19/layers/meta-st/meta-st-openstlinux  5
   meta                  /home/max/stm32mp1/openstlinux-20-02-19/layers/openembedded-core/meta  5
   meta-custom           /home/max/stm32mp1/meta-custom    7
   ```
   
  


## References

1. [Yocto Project Development Tasks Manual - Managing Layers](https://www.yoctoproject.org/docs/latest/dev-manual/dev-manual.html#managing-layers)
2. [How to create a new open embedded layer - stm32mpu](https://wiki.st.com/stm32mpu/wiki/How_to_create_a_new_open_embedded_layer)