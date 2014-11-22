---
layout: post
title: OpenCL on Samsung Chromebook 3
---

Here's quick description how to get going OpenCL on Samsung Chromebook 3.
-----

### Steps:

1. Install [crouton](https://github.com/dnschneid/crouton)

2. Install Mali drivers and SDK
    * Mali [driver](http://malideveloper.arm.com/develop-for-mali/drivers/mali-t6xx-gpu-user-space-drivers/http://malideveloper.arm.com/develop-for-mali/drivers/mali-t6xx-gpu-user-space-drivers/) - choose T604 X11 version 
    * Mali [SDK](http://malideveloper.arm.com/develop-for-mali/sdk/mali-opencl-sdk/)
    * I've installed the driver libraries into /usr/lib to avoid config-fu
    * Run sudo ldconfig
    * Modify library links to avoid symbol [lookup errors](http://krblogs.com/post/65347946766/mali-drivers-on-chromebook)

            ln -s /usr/lib/libmali.so /usr/lib/libOpenCL.so.1  
            ln -s /usr/lib/libOpenCL.so.1 /usr/lib/libOpenCL.so

3. Check permissions to the mali GPU device. I've added myself to the video group  

        ls -l /dev/mali0

4. Compile SDK examples to test if all is good.  [DrinkCat](http://drinkcat.blogspot.com/2013/11/opencl-on-samsung-chromebook-arm-under.html)
 has very good tutorial on this

5. Enjoy 1 node of [supercomputer](http://www.xbitlabs.com/news/other/display/20121119221057_Barcelona_Supercomputing_Center_Selects_Samsung_Exynos_Processor_for_Supercomputer.html) power

Credits:  
[DrinkCat](http://drinkcat.blogspot.com/2013/11/opencl-on-samsung-chromebook-arm-under.html)  
[Krishnaraj Bhat](http://krblogs.com/post/65347946766/mali-drivers-on-chromebook)
