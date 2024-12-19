---
layout: posts
title: 不开机改虚拟机IP
date: 2024-9-10 10:20:33
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "IP"
- "虚拟机"






---



```sh
#!/bin/bash

read -p "请输入虚拟机名称:" name
if virsh domstate $name | grep -q running; then
    echo "修改虚拟机网卡数据,需要关闭虚拟机，现在开始关机。"
    virsh destroy $name
fi

mountpoint="/vms/images/temp_vm_mount"
[ ! -d $mountpoint ] && mkdir $mountpoint
echo "请稍后..."
if mount | grep -q "$mountpoint"; then
    umount $mountpoint
fi

# # 使用具体的磁盘镜像路径
# disk_path=$(virsh domblklist $name | awk '/vda/ {print $2}')
# if [ -z "$disk_path" ]; then
#     echo "未找到虚拟机的磁盘镜像路径"
#     exit 1
# fi
# echo '使用具体的磁盘镜像路径'
# echo  $disk_path

# 挂载虚拟机的根文件系统
echo 'guestmount -d '$name' -i '$mountpoint
guestmount -d $name -i $mountpoint

read -p "请输入IP地址 (格式: xxx.xxx.xxx.xxx/xx):" addr
read -p "请输入网关地址:" gateway

# 创建新的 netplan 配置文件
new_netplan_file=$(mktemp)
cat <<EOF > $new_netplan_file
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens5:
      addresses:
      - $addr
      gateway4: $gateway
      nameservers:
        addresses:
        - 192.168.0.165
        search: []
  version: 2
EOF

# 替换虚拟机中的 netplan 配置文件
cp $new_netplan_file $mountpoint/etc/netplan/00-installer-config.yaml

# 检查新的配置文件是否已正确写入
if grep -q "$addr" $mountpoint/etc/netplan/00-installer-config.yaml && grep -q "$gateway" $mountpoint/etc/netplan/00-installer-config.yaml; then
    echo "完成..."
fi

# 卸载文件系统
umount $mountpoint

# 删除临时文件
rm $new_netplan_file

```

这是我工作写的，还比较方便用。