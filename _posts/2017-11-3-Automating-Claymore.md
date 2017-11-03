---
layout: post
title: Automating Claymore's Miner on Ubuntu / Debian
---

Crypto has always fascinated me ever since around 2011-2012 when I was first introduced. Maybe it was my 'edgy' libertarian leanings at the time but something about it always seemed righteous. Of course, as I type this Bitcoin is over 7000 dollars and all sorts of alt coins have risen and fallen, pumped and dumped, and scammed the world. 

I got into ethereum mining right before the huge circus around May 2017 where people everywhere were buying as many AMD GPU's they could get their hands on... Funnily enough, I would now say NVIDIA cards are far superior in terms of profit / cost (electricity) but thats a post for another time. I've got about 4 or 5 rigs right now still up and running and have recently co-located my rigs with a friend along with a pile of ASICS (Dash, Bitcoin, etc). We both work full time jobs and started running into scaling and maintenance problems. After banging my head against the keyboard a few too many times… it was time to automate:

![_config.yml]({{ site.baseurl }}/images/automation.png)

### Assumptions
* I mine on Linux, I know Microsoft is a better platform to mine on because of all the GPU tools but I refuse to pay the piper in terms of licensing. 

* We are working with Debian like systems (Ubuntu, Mint, etc). GPUs do not play very nice with RHEL,  CentOS etc and debian has well supported drivers and ‘just works’. 

* This post is also assuming AMD Radeon RX cards, I will update the master repository shortly with NVIDIA CUDA support. 

* You have ansible setup and configured. 

### Github Repo
You can find the github repo here: <https://github.com/bps12/Claymore-Miner-Ansible> - This repo is still a WIP

