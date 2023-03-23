# DMSETUP

## DMSETUP Tool Establishment

安裝 Oracle VM VirtualBox 及 Ubuntu14.04 版 
四個 1G disk
![](https://i.imgur.com/lqScGUG.jpg)

![](https://i.imgur.com/VTk1g7z.jpg)


## Steps for DM-cache Build-up

1GB+4MiB (1961317-sector)

```clike=
dmsetup create metadata --table '0 8192 linear /dev/sdb 0'
dmsetup create block --table '0 1953125 linear /dev/sdb 8192'
```

```clike=
sudo dd if=/dev/zero of=/dev/mapper/metadata
sudo dd if=/dev/zero of=/dev/mapper/block
```

用 lsblk 觀察目前 disk 的狀態
![](https://i.imgur.com/dPWK7Sm.jpg)
```clike=
sudo dmsetup create test --table '0  1961317 cache /dev/mapper/metadata /dev/mapper/block /dev/sdc 512 1 writeback default 0'
```

![](https://i.imgur.com/z8pssar.jpg)

![](https://i.imgur.com/yBaJ4mB.png)

建立檔案系統並將 test-linear mount 至 /root/mountpoint
```clike=
sudo mkfs -t ext3 -m 1 -v /dev/mapper/test-linear
sudo mount /dev/mapper/test-linear /root/mountpoint
```
![](https://i.imgur.com/O5UKmj1.png)

移至 mountpoint directory 並寫入一個 500M 的檔案
```clike=
sudo dd if=/dev/zero of=filename bs=1M count=500
```
可以看出 test 已被寫入 500M 的資料
![](https://i.imgur.com/sWx8Gdm.png)


## Create a Device-mapper Target with Linear Mapping 
四個 1G disk 合成一個 4G 的 test-linear with linear mapping 
```clike=
echo -e '0 1953125 linear /dev/loop0 8192'\\n'1953125 1953125 linear /dev/loop1 8192'\\n'3906250 1953125 linear /dev/loop2 8192'\\n'5859375 1953125 linear /dev/loop3 8192' | dmsetup create test
```
寫入 0 以免出錯
```clike=
sudo dd if=/dev/zero of=/dev/mapper/metadata
```
![](https://i.imgur.com/YEofMKT.jpg)

用 dmsetup status 檢查是否有成功建立
![](https://i.imgur.com/NqZqV4w.jpg)

建立檔案系統並將 test-linear mount 至 /root/mountpoint
```clike=
sudo mkfs -t ext3 -m 1 -v /dev/mapper/test
sudo mount /dev/mapper/test /root/mountpoint
```
可以看到 test-linear 已經成功建立
![](https://i.imgur.com/MiivuJR.jpg)

## Create a Device-mapper Target with RAID-5 Layout

照前述方法建置環境
![](https://i.imgur.com/ecjgnO7.jpg)

將四個硬碟合成成 raid 5
![](https://i.imgur.com/z409qd9.jpg)

最後用 dmsetup status 檢查是否建立成功
![](https://i.imgur.com/OU9PnI9.jpg)

## References
https://wiki.gentoo.org/wiki/Device-mapper
https://man7.org/linux/man-pages/man8/dmsetup.8.html
https://blog.csdn.net/xixihahalelehehe/article/details/115760829
