---
layout: post
title: "Create swap using ansible in Ubuntu"
date: 2017-02-22 07:03:12 +0530
comments: true
categories: [devops, ubuntu, swap, ansible]
---

This ansible script will help you to create SWAP memory in a single command.
Just change this line based on your required memory.

For 4 GB : `command: dd if=/dev/zero of=/swapfile bs=1G count=4`

For 2 GB : `command: dd if=/dev/zero of=/swapfile bs=1G count=2`

```bash Playbook command
ansible-playbook swap.yml -i inventorys/hosts --ask-pass
```

```yml swap.yml
- name: Create swap
  hosts: all
  remote_user: advp
  become: True
  become_method: sudo
  become_user: root
  gather_facts: True

  tasks:
	- name: Check whether swap is already enabled or not
	  shell: cat /etc/sysctl.conf 
	  register: swap_enabled

	- block:
	  - name: create swap file
	    command: dd if=/dev/zero of=/swapfile bs=1G count=4

	  - name: change permission type
	    file: path=/swapfile mode=600 state=file

	  - name: setup swap
	    command: mkswap /swapfile
	  
	  - name: create swap
	    command: swapon /swapfile

	  - name: Add to fstab
	    action: lineinfile dest=/etc/fstab regexp="swapfile" line="/swapfile none swap sw 0 0" state=present

	  - name: start swap
	    command: swapon -a

	  - name: set swapiness
	    sysctl:
	      name: vm.swappiness
	      value: "10"

	  - name: set swapiness
	    sysctl:
	      name: vm.vfs_cache_pressure
	      value: "50"

	  when: swap_enabled.stdout.find('swappiness') == -1
```