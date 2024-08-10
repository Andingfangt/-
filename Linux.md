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


