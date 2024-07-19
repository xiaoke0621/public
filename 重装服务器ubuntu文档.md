## 启动盘

1. **下载ubuntu镜像**

   官网：[Get Ubuntu | Download | Ubuntu](https://ubuntu.com/download)

   中科大源：[Index of /ubuntu-releases/20.04/ (ustc.edu.cn)](https://mirrors.ustc.edu.cn/ubuntu-releases/20.04/)

   下载后得到一个.iso后缀的文件。

2. **制作启动盘**

   首先下载一个软件[Rufus](http://rufus.ie/zh/)

   下载后打开，选中刚下载好的ubuntu镜像：

   ![image-20230904153615265](https://cdn.jsdelivr.net/gh/xiaoke0621/imgBeds@main//img/image-20230904153615265.png)

    

3. **设置u盘启动**

   以dell工作站为例，开机连按f12，选择从u盘进入。



## 系统安装及分区等

1. **系统安装**

   基本根据需求选择后一直continue，注意到这一步，

   选择【Erase disk and install Ubuntu】

   ![image-20230904155111808](https://cdn.jsdelivr.net/gh/xiaoke0621/imgBeds@main//img/image-20230904155111808.png) 

   一直到最后等待安装成功即可。

2. **硬盘分区、格式化、挂载等操作**

   Linux系统所有的设备都以文件的形式存储，硬盘设备一般保存在 /dev 目录下，以 sda、sdb 命名。Linux 系统使用任何硬件设备，都必须将设备文件与已有目录文件进行挂载。

   - sda：第一块硬盘，若对硬盘进行了分区，则有 sda1（第一个分区），sda2 等，一般是机器自带的硬盘；

   - sdb：第二块硬盘，一般为加装的移动硬盘。

     

   Ubuntu下挂载一个新硬盘的基本步骤是：

   1. 给硬盘创建分区；

   2. 给硬盘创建文件系统；

   3. 挂载移动硬盘。

      

   常用的一些指令：

   ```bash
   lsblk    #查看所有硬盘情况
   df -lh    #查看硬盘占用情况，以及挂载位置
   sudo fdisk -l    #查看硬盘分区情况
   sudo fdisk /dev/sdb    #给硬盘sdb创建分区
   sudo mkfs.ext4 /dev/sdb    #将硬盘sdb格式化为ext4格式，并写入文件系统
   sudo mount /dev/sdb /mnt/pan    #将硬盘sdb挂载到 /mnt/pan 目录
   ```

   

   **具体操作：**

   1. 查看硬盘情况

      ```bash
      df -lh
      #或者
      fdisk -l
      ```

      下面以`/dev/sdb1`为例

   2. 创建分区

      ```bash
      sudo fdisk /dev/sdb1
      ```
   
      依次输入：n（新建分区）、p、1（分区号）、回车、回车、w（保存退出），表示新建了一个分区，大小为整个sdb硬盘。可以输入m查看帮助信息。
   
      可以输入`sudo fdisk -l`查看是否创建。
   
   3. 将新分区格式化，并写入文件系统
   
      ```bash
      sudo mkfs -t ext4 /dev/sdb1
      ```
   
      硬盘需要先解挂才可以格式化。可以在图形界面解挂，也可以使用`umount`指令
   
   4. 创建临时目录（用来临时挂载新分区），将新分区挂载到临时文件夹
   
      ```bash
      sudo mkdir /mnt/newpart
      sudo mount /dev/sdb1 /mnt/newpart
      ```
   
      其实现在已经可以使用，但是想要把新硬盘挂载到`/home`，还需要接下来的操作。
   
   5. 将`/home`目录下的文件拷贝到新分区
   
      ```bash
      cd /home
      sudo cp -ax * /mnt/newpart
      ```
   
   6. 重新命名原`/home`目录
   
      ```bash
      cd /
      sudo mv /home /home.old
      sudo mkdir /home
      sudo mount /dev/sdb1 /home
      ```
   
   7. 查看uuid，找到新分区id
   
      ```bash
      sudo blkid
      ```
   
   8. 将新分区uuid加入`/etc/fstab`（设置开机自动挂载）
   
      ```bash
      sudo vim /etc/fstab
      #在最后一行空一行增加以下
      UUID=20984cef-05e4-44c1-bc12-758fc1ecd2e3 /home ext4 defaults 0 2
      ```
   
      
   
## Nvidia驱动安装

```bash
## 清除已安装的nvidia驱动
sudo apt-get purge nvidia*

## 加入gpu ppa
sudo add-apt-repository ppa:graphics-drivers

## 更新软件包
sudo apt-get update

## 查询支持的gpu版本
ubuntu-drivers list

## 从支持列表中安装指定版本
sudo apt install nvidia-driver-535

## 安装完成后，重启电脑后输入
nvidia-smi
### 若打印：gpu相关信息，则表示成功了。
### 若打印：“NVIDIA-SMI has failed because it could not communicate with the NVIDIA driver.” Make sure that the latest NVIDIA driver is installed and running.则表示失败
```

如果Ubuntu在登录界面出现输入密码后无限循环的情况，可以尝试安装nvidia驱动。



## tty模式

Ubuntu由于卸载软件不小心误删了系统图形界面；或者遇到开机输入密码无限循环的问题等等，可以尝试进入tty模式（虚拟终端）进行拯救。

进入tty的快捷键：`Ctrl+Alt+F1-F6`

F1:重新进入图形化界面

F2:返回当前的工作桌面

F3-F6:分别对应tty3-tty6，这些tty之间没有差别，只是防止一个终端登录不成功，切换其他登录即可

F7:显示分区情况，i.e./dev/nvme0n1p2:clean......blocks，这种情况下无法键入任何字符（暂时还不了解这个是干嘛用的），直接Ctrl+Alt+F1-F6进行账号登录切换即可

   



   

   

   

   

   

