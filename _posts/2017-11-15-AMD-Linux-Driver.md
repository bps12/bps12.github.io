---
layout: post
title: Testing AMD Beta Mining Drivers (17.40)
---

### Background
Mining ethereum and crypto in general can be an infinite rabbit hole. Ethereum has whats called a DAG file and DAG epochs. Essentially, its mathmatical goverence to keep Etehreum ASIC resistant. For the Math and CS majors you can find a detailed explanation [here](https://ethereum.stackexchange.com/questions/1993/what-actually-is-a-dag/8883#8883).

As this relates to Ethereum mining, your hash rate will go down the further along the DAG epochs are. You can find the current DAG epoch whenever you fire up your miner or check your mining pools homepage. This issue has already been patched with AMD Drivers for Windows but not in Linux until now. AMD's High performance Computing team has released the fix (to much rejoicing... or maybe just mine!). 

A few tweaks to your kernel can net you at least 20-40 MH/s with Ethereum.  


### The Install 
The fun starts [here](http://support.amd.com/en-us/kb-articles/Pages/AMDGPU-Pro-Beta-Mining-Driver-for-Linux-Release-Notes.aspx)

The directions are pretty self explanatory, upgrade or install the driver and reboot your system. You'll need to add the boot paramater amdgpu.vm_fragment_size=9 to the boot options in /etc/default/grub. This fix should only need to applied with Ellesmere cards (RX 480s / 580s). Vega cards set the 2MB fragments in the userspace automatically.  



### The Result
Before the fix, my 5 GPU rig was hashing at about 110 Mh/s whilist dual mining Sia. Here are the results after the above was applied:
![_config.yml]({{ site.baseurl }}/images/hash.png)

Happy Mining!



