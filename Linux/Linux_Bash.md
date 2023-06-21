

# Disk





## find

> 

```shell
find ~ -type f -name '*.BAK' -delete
```





## df

> disk filesystem

```shell
# 列出文件系统的类型。
$ df -T
# 查看磁盘剩余空间
$ df -hl 
# 查看每个根路径的分区大小
$ df -h  or df -m
# [目录名] 返回该目录的大小
$ du -sh 
# [文件夹] 返回该文件夹总M数
$ du -sm 

```







## du

> disk usage

```shell
# 要显示一个目录树及其每个子树的磁盘使用情况
du /home/kazeni

# 要通过以1024字节为单位显示一个目录树及其每个子树的磁盘使用情况
du -k /home/kazeni

# 以MB为单位显示一个目录树及其每个子树的磁盘使用情况
du -m /home/kazeni  

# GB为单位显示
du -g /home/kazeni  

# 查看当前目录下所有目录以及子目录的大小
du -h .  

#查看当前目录下user目录的大小，并不想看其他目录以及其子目录：
du -sh user

-s 表示总结的意思，即只列出一个总结的值
du -h --max-depth=0 user
--max-depth=n `表示只深入到第n层目录，此处设置为0，即表示不深入到子目录。`
du -h --max-depth=1

# 列出目录及其子目录下所有目录和文件的大小：
du -ah /home/kazeni
-a表示包括目录和文件

# 列出当前目录中的目录名不包括xyz字符串的目录的大小：
du -h --exclude='*xyz*'

# 想在一个屏幕下列出更多的关于user目录及子目录大小的信息：
du -0h user
-0（杠零）表示每列出一个目录的信息，不换行，而是直接输出下一个目录的信息。

# 只显示一个目录树的全部磁盘使用情况
du -s /home/kazeni


常用参数：
-a或-all 为每个指定文件显示磁盘使用情况，或者为目录中每个文件显示各自磁盘使用情况。
-b或-bytes 显示目录或文件大小时，以byte为单位。
-c或–total 除了显示目录或文件的大小外，同时也显示所有目录或文件的总和。
-D或–dereference-args 显示指定符号连接的源文件大小。
-h或–human-readable 以K，M，G为单位，提高信息的可读性。
-H或–si 与-h参数相同，但是K，M，G是以1000为换算单位,而不是以1024为换算单位。
-k或–kilobytes 以1024 bytes为单位。
-l或–count-links 重复计算硬件连接的文件。
-L<符号连接>或–dereference<符号连接> 显示选项中所指定符号连接的源文件大小。
-m或–megabytes 以1MB为单位。
-s或–summarize 仅显示总计，即当前目录的大小。
-S或–separate-dirs 显示每个目录的大小时，并不含其子目录的大小。
-x或–one-file-xystem 以一开始处理时的文件系统为准，若遇上其它不同的文件系统目录则略过。
-X<文件>或–exclude-from=<文件> 在<文件>指定目录或文件。
–exclude=<目录或文件> 略过指定的目录或文件。
–max-depth=<目录层数> 超过指定层数的目录后，予以忽略。
–help 显示帮助。
–version 显示版本信息。
```



## fdisk

`heads` 磁头、`sectors/track` 扇区、`cylinders` 柱面



```shell

# View Specific Disk Partition in Linux
$ fdisk -l /dev/sda

Check all Available fdisk Commands
$ fdisk /dev/sda


# How to Delete a Partition in Linux
$ fdisk /dev/sda
WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
switch off the mode (command 'c') and change display units to
sectors (command 'u').
Command (m for help): d
Partition number (1-4): 4
Command (m for help): w
The partition table has been altered!
Calling ioctl() to re-read partition table.
WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
You have new mail in /var/spool/mail/root


# How to Create a New Partition in Linux
$ fdisk /dev/sda


```







## 其他

> 挂载windows共享文件夹

```shell
# 非ubuntu 则用yum安装
$ sudo apt-get install cifs-utils
$ sudo mount //WINSRV/SHARE -t cifs -o uid=1000,gid=1000,username=winuser /mnt/path

```



# tar

tar 命令使用了稍微有点奇怪的方式来表达它的选项，所以我们需要一些例子来展示它是 怎样工作的。首先，让我们重新创建之前我们用过的操练场:

```shell
$ mkdir -p playground/dir-{00{1..9},0{10..99},100}
$ touch playground/dir-{00{1..9},0{10..99},100}/file-{A..Z}

# 创建整个操练场的 tar 包：
$ tar cf playground.tar playground/
# 列出归档文件的内容
$ tar tf playground.tar
# 更详细的列表信息
$ tar tvf playground.tar

# 提取文件
$ tar xf playgroud.tar
```





# ps

```shell
# Sort ‘ps’ output by memory (RAM), from high to low
$ ps aux --sort -rss
$ ps aux --sort rss
```





#  sort



| 选项   | 长选项                     | 描述                                       |
| ---- | ----------------------- | ---------------------------------------- |
| -b   | --ignore-leading-blanks | 默认情况下，对整行进行排序，从每行的第一个字符开始。这个选项导致 sort 程序忽略 每行开头的空格，从第一个非空白字符开始排序。 |
| -f   | --ignore-case           | 让排序不区分大小写。                               |
| -n   | --numeric-sort          | 基于字符串的长度来排序。使用此选项允许根据数字值执行排序，而不是字母值。     |
| -r   | --reverse               | 按相反顺序排序。结果按照降序排列，而不是升序。                  |
| -k   | --key=field1[,field2]   | 对从 field1到 field2之间的字符排序，而不是整个文本行。看下面的讨论。 |
| -m   | --merge                 | 把每个参数看作是一个预先排好序的文件。把多个文件合并成一个排好序的文件，而没有执行额外的排序。 |
| -o   | --output=file           | 把排好序的输出结果发送到文件，而不是标准输出。                  |
| -t   | --field-separator=char  | 定义域分隔字符。默认情况下，域由空格或制表符分隔                 |



 	

```shell
# 显示 10 个最大的空间消费者
$ du -s /usr/share/* | sort -nr | head
```