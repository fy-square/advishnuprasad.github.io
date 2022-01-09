---
layout: post
title: "Setup NFS server and client using ansible"
date: 2016-03-29 18:16:12 +0530
comments: true
categories: [ansible, devops, nfs, shared_file_system]
---

### Setup NFS server and client using ansible

If you have a centralized server and you want to share a disk from the server, the best way is
to use NFS model.

![API docs image]({{ site.url }}/images/nfs.png)

You might have to create a server with enough disk space. Let's say you have a disk with file system
as `/dev/xvdb` and the size is 100 GB.

Now you want to share this volume with other machines. Below is the script to do it using ansible.

Read [this](https://help.ubuntu.com/community/SettingUpNFSHowTo) tutorial before writing ansible script.

### Ansible script

{% raw %}
```yml inventory.yml
[nfs_server]
10.0.0.1

[nfs_clients]
10.0.0.2
10.0.0.3
```

```bash exports.j2
# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/nfs            10.0.0.1/24(rw,sync,no_root_squash,no_subtree_check)
```

```yml nfs-server.yml
---
- hosts: nfs_server
  remote_user: ubuntu
  sudo: yes

  tasks:
    - name: Create mountable dir
      file: path=/share state=directory mode=777 owner=root group=root

    - name: make sure the mount drive has a filesystem
      filesystem: fstype=ext4 dev={{ mountable_share_drive | default('/dev/xvdb') }}

    - name: set mountpoints
      mount: name=/share src={{ mountable_share_drive | default('/dev/xvdb') }} fstype=auto opts=defaults,nobootwait dump=0 passno=2 state=mounted

    - name: Ensure NFS utilities are installed.
      apt: name={{ item }} state=installed update_cache=yes
      with_items:
        - nfs-common
        - nfs-kernel-server

    - name: copy /etc/exports
      template: src=exports.j2 dest=/etc/exports owner=root group=root

    - name: restart nfs server
      service: name=nfs-kernel-server state=restarted

```

```yml nfs_clients.yml
- hosts: nfs_clients
  remote_user: ubuntu
  sudo: yes

  tasks:
    - name: Ensure NFS common is installed.
      apt: name=nfs-common state=installed update_cache=yes

    - name: Create mountable dir
      file: path=/nfs state=directory mode=777 owner=root group=root

    - name: set mountpoints
      mount: name=/nfs src={{hostvars[groups['nfs_server'][0]]['ansible_eth0']['ipv4']['address']}}:/share fstype=nfs opts=defaults,nobootwait dump=0 passno=2 state=mounted
```

{% endraw %}
