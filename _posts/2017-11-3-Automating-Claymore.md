---
layout: post
title: Automating Claymore's Miner on Ubuntu / Debian
---

Crypto has always fascinated me ever since around 2011-2012 when I was first introduced. Maybe it was my 'edgy' libertarian leanings at the time but something about it always seemed righteous. Of course, as I type this Bitcoin is over 7000 dollars and all sorts of alt coins have risen and fallen, pumped and dumped, and scammed the world. 

I got into ethereum mining right before the huge circus around May 2017 where people everywhere were buying as many AMD GPU's they could get their hands on... Funnily enough, I would now say NVIDIA cards are far superior in terms of profit / cost (electricity) but thats a post for another time. I've got about 4 or 5 rigs right now still up and running and have recently co-located my rigs with a friend along with a pile of ASICS (Dash, Bitcoin, etc). We both work full time jobs and started running into scaling and maintenance problems. After banging my head against the keyboard a few too many times… it was time to automate:

![_config.yml]({{ site.baseurl }}/images/automation.png)

### Assumptions
I mine on Linux, I know Microsoft is a better platform to mine on because of all the GPU tools but I refuse to pay the piper in terms of licensing. 

We are working with Debian like systems (Ubuntu, Mint, etc). GPUs do not play very nice with RHEL,  CentOS etc and debian has well supported drivers and ‘just works’. 

This post is also assuming AMD Radeon RX cards, I will update the master repository shortly with NVIDIA CUDA support. 

You have ansible setup and configured. 

### Main Playbook & vars


```---
# Set Claymore destiation file
claymore_dest_dir: /usr/local/Claymore10
```

```---

- hosts: miner
  roles:
  - miner
  remote_user: root

  vars_prompt:
  - name: "prompt_claymore_url"
    prompt: Enter Claymore Miner URL
    private: no
  - name: "prompt_amd_drivers"
    prompt: Enter AMD driver URL
    private: no
```


### All Tasks

```---
- include: upgrade.yml
- include: minedl.yml
- include: opencl.yml
```

### Upgrade task

```---
- name: Apt update / upgrade
  apt: upgrade=yes update_cahce=yes
  become: yes

- name: Install extra packages
  apt: pkg={{ item }}
  become: yes
  with_items:
    - git
    - tmux
    - opencl-headers
    - htop
    - screen
    - openssh-server
```

### Claymore Miner task
```---

- name: download Claymore Miner
  get_url:
    url: "{{ prompt_url }}"
    dest: /tmp/
- name: Create Claymore10 directory
  file:
    path: {{ claymore_dest_dir }}
    state: directory
    owner: root
    group: root

- name: Unarchive Claymore Miner
  raw: tar zxvf /tmp/Claymore.s.Dual.Ethereum.Decred_Siacoin_Lbry_Pascal.AMD.NVIDIA.GPU.Miner.v10.0.-.LINUX.tar.gz -C {{claymore_dest_dir}}

- name: add sticky permissions so every user can mine
  file:
    path: {{ claymore_dest_dir }}/ethminerdcr64
    mode: "u+s"

- name: copy claymore scripts
  copy:
      src: "{{ item }}"
      dest: "{{ claymore_dest_dir }}"
  with_items:
    - "reboot.sh"
    - "mine.sh"

- name: add executable permissons on mine.sh and reboot.sh
  file:
      src: "{{ claymore_dest_dir }}/{{ item }}"
      mode: "u+x"
  with_items:
    - "reboot.sh"
    - "mine.sh"

- name: install claymore service
  include_role:
    name: systemd-service
  vars:
    systemd_service_name: "claymore"
    systemd_service_envs:
      - "WORKER={{inventory_hostname_short}}"
      - "GPU_MAX_ALLOC_PERCENT=100"
    systemd_service_Unit_Description: Claymore Miner
    systemd_service_Unit_Requires: network.target
    systemd_service_Unit_After: network.target
    systemd_service_Service_ExecStart: "{{claymore_dest_dir}}/mine.sh"

- name: enable claymore service
  file:
    src: /etc/systemd/system/claymore.service
    dest: /etc/systemd/system/multi-user.target.wants/claymore.service
    owner: root
    group: root
    state: link
```


### OpenCL Driver task
```---

- name: Check if amdgpu-pro is installed
  command: dpkg-query -W amdgpu-pro
  register: amdgpu_pro_check_deb
  failed_when: amdgpu_pro_check_deb.rc > 1
  changed_when: amdgpu_pro_check_deb.rc =

- name: Get openCL drivers
  get_url:
    url: {{ prompt_amd_drivers }}
    dest: /tmp

- name: untar openCL
  raw: tar -Jxvf amdgpu-pro-17.40-492261.tar.xz

- name: run the script
  script: /tmp/amdgpu-pro-17.40-492261/amdgpu-pro-install -y

- name: Add permissions for video user
  raw: sudo usermod -a -G video $LOGNAME

# reboot to apply openCL drivers and wait with the ssh connection
- name: Reboot the server for kernel update
  shell: ( sleep 3 && /sbin/reboot & )
  async: 0
  poll: 0

- name: Wait for the server to reboot
  local_action: wait_for host="{{invetory_hostname}}" search_regex=OpenSSH port=22 timeout=300
```

### Mine.sh file
```#!/bin/sh
#ALL CLAYMORE OPTIONS CAN BE FOUND IN README FILE!
export GPU_MAX_ALLOC_PERCENT=100
export GPU_MAX_HEAP_SIZE=100
export GPU_USE_SYNC_OBJECTS=1
export GPU_SINGLE_ALLOC_PERCENT=100
./ethdcrminer64 -epool eth-us-east1.nanopool.org:9999 -ewal ---YOURWALLETHERE--- -epsw x  -tt 70 -ttdcr 80 -tstop 90 -fanmin 65 -allpools 1 -r 1
```

### Reboot.sh file
```#!/bin/sh
reboot -f
```
You can find the github repo here: <https://github.com/bps12/Claymore-Miner-Ansible> - This repo is still a WIP

