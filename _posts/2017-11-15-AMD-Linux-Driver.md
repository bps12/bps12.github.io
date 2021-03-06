---
layout: post
title: Testing AMD Beta Linux Mining Drivers (17.40)
comments: true
---

### Background
Mining Ethereum and crypto in general can be an infinite rabbit hole. Ethereum has whats called a DAG file and DAG epochs. Essentially, its mathematical governance to keep Ethereum ASIC resistant. For the math and CS majors you can find a detailed explanation [here](https://ethereum.stackexchange.com/questions/1993/what-actually-is-a-dag/8883#8883).

As this relates to Ethereum mining, your hash rate will go down the further along the DAG epochs are(You can find the current DAG epoch whenever you fire up your miner or check your mining pools homepage). This issue has already been patched with AMD drivers for Windows but not in Linux until now. AMD's high performance computing team has released the fix (to much rejoicing... or maybe just mine!). 

A few tweaks to your kernel can net you at least 20-40 MH/s.


### The Install 
You can find the instructions [here](http://support.amd.com/en-us/kb-articles/Pages/AMDGPU-Pro-Beta-Mining-Driver-for-Linux-Release-Notes.aspx).

I have additionally heard rumblings on bitcointalk and discord that the ROCm kernel is also required for this fix. I was already running this kernel on my miners testing out some ways to process media at scale for future VDI deployments. 

The directions are pretty self explanatory, upgrade or install the driver and reboot your system. You'll need to add the boot paramater amdgpu.vm_fragment_size=9 to the boot options in /etc/default/grub. This fix should only need to applied with Ellesmere cards (RX 480s / 580s). Vega cards set the 2MB fragments in the userspace automatically.  



### The Result
Before the fix my 5 GPU rig was hashing at about 110 Mh/s whilist dual mining Sia. Here are the results after the above was applied:
![_config.yml]({{ site.baseurl }}/images/hash.png)

Happy Mining!



