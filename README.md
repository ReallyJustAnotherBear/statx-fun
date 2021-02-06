This I cloned and added a entry for the arm 32bit syscall to see If I can reuse to get start getting file creation times for files created on ext4, on systems with older glibc. Sample imported populates birth/crtime. This would be useful to tag video file creation times for me in my hls project.

```
root@raspberrypi:~/statx-fun# uname -a
Linux raspberrypi 5.4.83-v7l+ #1379 SMP Mon Dec 14 13:11:54 GMT 2020 armv7l GNU/Linux
```
```
Raspberry Pi reference 2021-01-11
Generated using pi-gen, https://github.com/RPi-Distro/pi-gen, 21090519d85bdaa1615d5d5057d37b09368ea5d2, stage2
```
```
root@raspberrypi:~/statx-fun# cat /etc/debian_version 
10.7
```

I added an entry for __arm__ and then.

```
root@raspberrypi:~/statx-fun# make
cc -c -Wall statx.c -o statx.o
statx.c:55: warning: "__NR_statx" redefined
     #define __NR_statx 397
 
In file included from /usr/include/arm-linux-gnueabihf/asm/unistd.h:27,
                 from /usr/include/arm-linux-gnueabihf/sys/syscall.h:24,
                 from statx.c:39:
/usr/include/arm-linux-gnueabihf/asm/unistd-common.h:356: note: this is the location of the previous definition
 #define __NR_statx (__NR_SYSCALL_BASE + 397)
 
cc -o statx -Wall statx.o
root@raspberrypi:~/statx-fun# ./statx ./statx.c
Filename: ./statx.c
Mask: fff
results=fff
  Size: 8010            Blocks: 16         IO Block: 4096    regular file
Device: b3:02           Inode: 136289      Links: 1    
Access: (0644/-rw-r--r--)  Uid:     0   Gid:     0
Access: 2021-02-05 17:47:51.513774271-0800
Modify: 2021-02-05 17:47:51.513774271-0800
Change: 2021-02-05 17:47:51.513774271-0800
 Birth: 2021-02-05 17:47:51.513774271-0800
Attributes: 0000000000000000 (........ ........ ........ ........ ........ ........ ....-... .---.-..)

```

Updated:
#after reviewing the warning above for 32bit arm, I just added asm/unistd.h 

```
#elif __arm__
  #include <asm/unistd.h>
```
because it can pull it in from unistd-common.h
Now no more warnings for the arm option and I don't have to override the existing value in raspbian.
Don't know which is most correct, but I'll use any others first before redefining, until informed or I learn otherwise.









```





# statx

This project is an example implementation of the statx()-systemcall which was introduced in Linux 4.11

## Requirements

statx-fun was written for x86, arm64 and x86_64 architectures. but it just takes two lines of code(place the right syscall-number) to port it to other architectures. It requires at least Linux Kernel 4.11.

Please note, that at the moment there is no glibc wrapper for statx()-systemcall.

## Compile

```
make
```

## Usage

### Basic Usage

```
./statx ./statx.c
Filename: statx.c
Mask: fff
results=fff
  Size: 7313            Blocks: 16         IO Block: 4096    regular file
Device: fe:02           Inode: 10227350    Links: 1    
Access: (0644/-rw-r--r--)  Uid:  1000   Gid:  1000
Access: 2017-06-02 09:51:32.982941917+0200
Modify: 2017-06-02 09:48:16.313825618+0200
Change: 2017-06-02 09:48:16.321825666+0200
 Birth: 2017-06-02 09:48:16.313825618+0200
Attributes: 0000000000000000 (........ ........ ........ ........ ........ ........ ....-... .---.-..)

```
### Help

```
./statx
usage: ./statx [OPTION]... [FILE]...

options:
	-a.....no automount
	-l.....dereference links
	-b.....basic stats
	-s.....get filesize only
	-f.....force sync(remote fs)
	-d.....don't sync(remote fs)
	-h.....help
```
## Documentation
   * [Man-Page](https://www.mankier.com/2/statx)
   * Kernel 4.11 Source-Tree (see samples/statx/test-statx.c)

## Licence

GPL

## Author Information

Wolfgang Hotwagner [**(https://tech.feedyourhead.at/)**](https://tech.feedyourhead.at)
