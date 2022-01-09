---
layout: post
title: "Encryption at REST"
date: 2016-09-24 22:04:32 +0530
comments: true
categories: [devops, ubuntu, data, encryption]
---

Already so many people have written about what/why Encryption at REST. 
So I am just going to help you to setup Encryption at REST in ubuntu using luksFormat. 
You can read more about the cryptsetup and LuksFormat [here](https://wiki.archlinux.org/index.php/Dm-crypt/Device_encryption)

### Step 1: Create crypt volume file format

Assuming the device is `/dev/xvdc`. Please do not forget the passphrase. You will not be able to recover the data if you forget.

```bash
sudo su
cryptsetup -y -v luksFormat /dev/xvdc
```

### Step 2:
```bash
cryptsetup luksOpen /dev/xvdc edata-epg
```

### Step 3:
```bash
cryptsetup -v status edata-epg
```

Step 4:
```bash
apt-get install pv
pv -tpreb /dev/zero | dd of=/dev/mapper/edata-epg bs=128M
```

Step 5:
```bash
mkfs.ext4 /dev/mapper/edata-epg
mount /dev/mapper/edata-epg /e_data
```