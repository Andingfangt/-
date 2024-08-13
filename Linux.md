## linux下的rsync（文件同步）用法

```Shell
[root@localhost ~]rsync /etc/fstab /tmp                # 在本地同步
[root@localhost ~]rsync -r /etc 172.16.10.5:/tmp       # 将本地/etc目录拷贝到远程主机的/tmp下，以保证远程/tmp目录和本地/etc保持同步
[root@localhost ~]rsync -r 172.16.10.5:/etc /tmp       # 将远程主机的/etc目录拷贝到本地/tmp下，以保证本地/tmp目录和远程/etc保持同步
[root@localhost ~]rsync /etc/                          # 列出本地/etc/目录下的文件列表
[root@localhost ~]rsync 172.16.10.5:/tmp/              # 列出远程主机上/tmp/目录下的文件列表
```

**源路径如果是一个目录的话，带上尾随斜线和不带尾随斜线是不一样的，不带尾随斜线表示的是整个目录包括目录本身，带上尾随斜线表示的是目录中的文件，不包括目录本身。**

## linux下怎么创建、复制、移动、删除文件和目录

```bash
# 创建目录
mkdir mydir
# 创建文件
touch myfile.txt
# 复制文件到目录
cp myfile.txt mydir/
# 复制目录
cp -r mydir mydir_copy
# 移动文件并重命名
mv myfile.txt newfile.txt
# 移动目录并重命名
mv mydir mydir_new
# 删除文件
rm newfile.txt
# 删除目录
rm -r mydir_new
# 强制删除目录及其内容
rm -rf mydir_copy
```

<a name="pipline"></a>

## Linux 管道

管道的本质就是内核在内存中开辟了一个缓冲区，这个缓冲区与管道文件相关联，对管道文件的操作，被内核转换成对这块缓冲区的操作。

**匿名管道:**

* 使用竖线 | 连接多个命令，这被称为管道符 : `$ command1 | command2`
* 管道也是文件。在使用管道符 "|" 时， "|" 左边的进程以写的方式打开管道文件，将标准输出重定向到管道之中， "|" 右边的进程以读的方式打开管道文件，将标准输入重定向到管道之中。
* 通过管道符 | 创建的管道是匿名管道，用完了就会被**自动销毁**。并且，匿名管道只能在具有**亲缘关系**（父子进程）的进程间使用。
* **「管道中的数据只能单向流动」**，也就是**半双工通信**，如果想实现相互通信（全双工通信），我们需要创建两个管道才行，一个方向一个。

**命名管道：**

使用 Linux 命令 mkfifo 来创建有名管道： `$ mkfifo mypipe`

* 有名管道，也称做**FIFO**，因为数据是先进先出的传输方式。
* 所谓有名管道也就是提供一个路径名与之关联，这样，即使与创建有名管道的进程不存在亲缘关系的进程，只要可以访问该路径，就能够通过这个有名管道进行相互通信。
