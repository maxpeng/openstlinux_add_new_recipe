
# OpenSTLinux - Creating a recipe for a git source tree

## Introduction

Last month one of my friends, who is a FAE of STMicroelectronics, told me that  STMicroelectronics released a new [STM32MP1 series microprocessor](https://www.st.com/en/microcontrollers-microprocessors/stm32mp1-series.html) last year. Each of STM32Mp1 series microprocessor has Arm Cortex-A7 and Cortex-M4 cores, therefore we could run a Linux OS on it. Since my friend knows that I am pretty interested in Linux, he suggested me to take a look that new product.

![STM32MP157C-DK2](https://www.st.com/bin/ecommerce/api/image.PF267415.en.feature-description-include-personalized-no-cpn-medium.jpg)

Consequently I googled the product information about STM32MP1, and I found  STMicroelectronics has released some development boards for it. One of the board, which is [STM32MP157C-DK2 discovery board], attracted my eye.

The reasons why the board attracted my eye are:

1. The board comes with a LCD panel. It could be useful to monitor what happens to the board.
2. The price of the board is only US$99.
3. STMicroelectronics provides [STM32 MPU OpenSTLinux Distribution](https://www.st.com/en/embedded-software/stm32-mpu-openstlinux-distribution.html) for the board, which is a Linux distribution based on [Yocto](https://www.yoctoproject.org/)/[OpenEmbedded](https://wiki.st.com/stm32mpu/wiki/OpenEmbedded) build systems.
4. The best part is STMicroelectronics provides a [STM32 MPU wiki](https://wiki.st.com/stm32mpu). The wiki provides a lot of practical information about how to learn and customize the OpenSTLinux distribution through Yocto/OpenEmbedded build system.

I have played [STM32MP157C-DK2 discovery board] for a while, I would like to share what I learned about creating a recipe by Yocto/OpenEmbedded build systems.

## Overview

The post of "`OpenSTLinux - Creating a recipe for a git source tree`" is split
into few parts as:
1. [Part 1 - Creating the recipe by devtool](create_the_recipe_by_devtool.md) - this part describes how to use `devtool` to create a recipe from a source tree checked out from a git repository.
2. [Part 2 - Using bitbake-layers to add the recipe to the build environment](add_the_layer_by_bitbake-layers.md) - this part describes how to use `bitbake-layers` to create a layer, populate the recipe to that layer, and finally add the layer to the build environment.

Let's start the first topic - [Part 1 - Creating the recipe by devtool](create_the_recipe_by_devtool.md).

[STM32MP157C-DK2 discovery board]: https://www.st.com/en/evaluation-tools/stm32mp157c-dk2.html
