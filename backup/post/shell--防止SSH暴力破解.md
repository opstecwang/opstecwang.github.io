---
title: "Shell | 防止SSH暴力破解"
tags: ["shell"]
categories: ["脚本"]
date: 2021-06-07T10:10:00+08:00
toc: true
draft: false
---

```shell
#! /bin/bash
# name:ssh_deny.sh
cat /var/log/secure | awk '/Failed/ {print $(NF-3)}' | sort | uniq -c | awk '{print $2"="$1}' > /root/black.txt
DEFINE="100"
 
for i in `cat /root/black.txt`
do
    IP=`echo $i | awk -F= '{print $1}'`
    NUM=`echo $i | awk -F= '{print $2}'`
    
    if [ $NUM -gt $DEFINE ];
    then
        grep $IP /etc/hosts.deny > /dev/null
        
        if [ $? -gt 0 ];
        then
            echo "sshd:$IP" >> /etc/hosts.deny
        fi
    fi
done
```

