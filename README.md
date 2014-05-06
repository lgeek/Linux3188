Kernel patches for enabling hardware 2D acceleration on RK3188
==============================================================

What?
-----

I'm working on adding some 2D graphics acceleration for Xorg running on the RK3188 SoC. There are various low cost mini computers based on this SoC which provide excellent value for money, but GNU/Linux isn't officially supported. The work done by the community allows us to run it, but the GUI can sometimes feel slow and software scaling of video is just way too slow.

At the moment, this repository contains some kernel patches required to use hardware acceleration. The Xorg driver based on [xf86-video-fbturbo](https://github.com/lgeek/xf86-video-fbturbo) will eventually be available [here](https://github.com/ssvb/xf86-video-fbturbo). I intend to get my contributions merged in the upstream channels ASAP.

Build notes
-----------

There seems to be a bug causing some builds to hang while doing the initial setup of the DDR controller. This isn't caused by my patches and I haven't yet figured out the exact cause. Whether this happens or not seems to depend entirely on the toolchain. 

For cross-compiling, a version known to produce working results is gcc (arm-none-eabi-) 4.5.1, shipped with [CodeSourcery G++ Lite](http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/editions/lite-edition/) 2010.09-51, but I wouldn't really recommend such an old version (it's just something I had set up on my machine).

gcc (arm-none-eabi-) 4.9.0 from the Arch repository is known to produce bad builds.

Let me know if builds produced by recent releases of the arm-linux-gnueabihf- port work. Other ways to contribute would be by debugging the cause of the problem or by submitting a patch.

Users
-----

Since I haven't yet published the Xorg driver, this repository is basically useless for you at this point. Please come back later for updates. :)

Sorry, I don't have the time to provide any support. Bug reports to the [issue tracker](https://github.com/lgeek/Linux3188/issues) are welcomed, but please don't submit generic support requests.


Developers
----------

Patches and pull requests are most welcome, but please keep them high quality: 
* descriptive commit messages
* only related work in a single commit or pull request
* don't include your .config file or files generated at build time
* follow the coding conventions around the code you're modifying (I know some of the RK-specific code is quite messy, let's not make it worse)
* if possible, use git to merge other people's patches, don't just copy the files making your own commits

If you maintain your own kernel tree for RK3188, please consider merging my patches, especially the ones in the *for_upstream* branch. The various forks of this kernel are quite fragmented, which is why I'm offering to merge here any acceptable patches, not just things related to 2D graphics acceleration.


Organisation of branches
------------------------
* *master* - includes all my patches and any patches contributed by other developers, including formatting/style/cleanup patches. It should always be possible to build a working kernel from this branch, but treat it as experimental.
* *for_upstream* - only my functional patches which should be ready for use and merging in other kernel trees.
* *prX* - temporary branches used for pull requests. Github has this silly idea that only complete branches should be submitted with pull requests. This doesn't work so well since different unrelated patches should be discussed separately. A temporary workaround is to have temporary branches in which I cherry pick one commit or set of related commits at a time. TLDR: just ignore these.
* others - feature branches which are not yet ready for *master*


List of functional changes
--------------------------
* [rk_fb, rk*_lcdc: Return -NOIOCTLCMD for unsupported ioctl cmds](https://github.com/lgeek/Linux3188/commit/6ecc29c50e7eb7f583c1195ee5e8fc6d61f0a3f5) - required to stop fbturbo mistakenly think that Raspberry Pi DMA accelerated framebuffer blits are supported
* [LCDC: Don't call winX_open if already in correct state](https://github.com/lgeek/Linux3188/commit/b415621143c8ac9e800bc280668f4aea1c0c0d40) - stops the LCDC from being powered off when an LCDC layer (window) is turned on or off multiple times
* [RK3188 LCDC: use win1 for the base fb layer](https://github.com/lgeek/Linux3188/commit/3fff78f0441f4e21e5831e614d17d42117280529) - makes the framebuffer use the graphics layer by default, freeing up the video layer to be used by the XV driver
* [RK FB: make screen-centered scaling optional](https://github.com/lgeek/Linux3188/commit/d0444e580a32adf1890d99c1691153199475333f) - enables arbitrary positioning and scaling of the video layer, for use by the XV driver
* [RGA: use uninterruptible sleep for ioctls](https://github.com/lgeek/Linux3188/commit/c6f5730a30b3de6727cf31be0ba2ae7c59db5e7f) - bugfix and performance improvement for RGA, which is used for hardware accelerated framebuffer-to-framebuffer blits
* [RK3188 LCDC: add support for hardware cursor](https://github.com/lgeek/Linux3188/commit/9a2c02fb0072482be80baf8617d6fd583215efd7) - the hardware cursor is required because it can show up above the video layer. It might also come with slight performance benefits since it's completely rendered by hardware, above the framebuffer.
* [RK FB: add ioctl for reading fb addresses and length](https://github.com/lgeek/Linux3188/commit/ac210001b05b3df3bb351f043f1391b9a12d199b) - used to allocate off-screen framebuffer memory for temporary RGA buffers and for the video layer


aloksinha's README starts below

---------------------------------------

Linux3188

Finally a complete kernel build.

Build tested on MK802IV, config file updated

========
Linux Source - RK3188 - PicUntu

We are making progress.

The current code works... for developers though.

The development team is working hard to get this working for normal users as well.

Thanks to Galland, the Linux console and the MALI support is operational.

More updates soon...

Alok


