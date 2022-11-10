### Linux syscall fundamentals
The goal of this repository is to teach how we interact with Linux kernel. To help with this task, lets create a `c` language binary. Assume that the source code is stored in a file called 'hello.c'. To compile the file 'hello.c' with gcc, use the following command:
```
$ sudo apt install gcc -y
$ gcc -Wall hello.c -o hello
$ ls
hello  hello.c
$ ./hello
Hello, world!
``` 
This compiles the source code in 'hello.c' to machine code and stores it in an executable file 'hello'. The option -Wall turns on all the most commonly-used compiler warnings---it is recommended that you always use this option.
```
$ strace -f ./hello
execve("./hello", ["./hello"], 0x7ffdd8664598 /* 26 vars */) = 0
brk(NULL)                               = 0x55e57530a000
arch_prctl(0x3001 /* ARCH_??? */, 0x7ffd8d47bb40) = -1 EINVAL (Invalid argument)
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7efd3ef8c000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=23523, ...}, AT_EMPTY_PATH) = 0
mmap(NULL, 23523, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7efd3ef86000
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\237\2\0\0\0\0\0"..., 832) = 832
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
pread64(3, "\4\0\0\0 \0\0\0\5\0\0\0GNU\0\2\0\0\300\4\0\0\0\3\0\0\0\0\0\0\0"..., 48, 848) = 48
pread64(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0i8\235HZ\227\223\333\350s\360\352,\223\340."..., 68, 896) = 68
newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=2216304, ...}, AT_EMPTY_PATH) = 0
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
mmap(NULL, 2260560, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7efd3ed5e000
mmap(0x7efd3ed86000, 1658880, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x28000) = 0x7efd3ed86000
mmap(0x7efd3ef1b000, 360448, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1bd000) = 0x7efd3ef1b000
mmap(0x7efd3ef73000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x214000) = 0x7efd3ef73000
mmap(0x7efd3ef79000, 52816, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7efd3ef79000
close(3)                                = 0
mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7efd3ed5b000
arch_prctl(ARCH_SET_FS, 0x7efd3ed5b740) = 0
set_tid_address(0x7efd3ed5ba10)         = 15082
set_robust_list(0x7efd3ed5ba20, 24)     = 0
rseq(0x7efd3ed5c0e0, 0x20, 0, 0x53053053) = 0
mprotect(0x7efd3ef73000, 16384, PROT_READ) = 0
mprotect(0x55e574949000, 4096, PROT_READ) = 0
mprotect(0x7efd3efc6000, 8192, PROT_READ) = 0
prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0
munmap(0x7efd3ef86000, 23523)           = 0
newfstatat(1, "", {st_mode=S_IFCHR|0620, st_rdev=makedev(0x88, 0x1), ...}, AT_EMPTY_PATH) = 0
getrandom("\xd2\xbd\x25\x1b\xdc\x50\xbd\xb8", 8, GRND_NONBLOCK) = 8
brk(NULL)                               = 0x55e57530a000
brk(0x55e57532b000)                     = 0x55e57532b000
write(1, "Hello, world!\n", 14Hello, world!
)         = 14
exit_group(0)                           = ?
+++ exited with 0 +++
```
Almost every line in the code snippet above are `syscalls`, we could easily examin any of them just by running man command, for example:
```
$ man newfstatat
DESCRIPTION
       These  functions  return information about a file, in the buffer pointed to by statbuf.  No permissions are required on the file itself,
       but—in the case of stat(), fstatat(), and lstat()—execute (search) permission is required on all of the  directories  in  pathname  that
       lead to the file.
...
```
Additionally we could check shared libraries (so files) used by our program.
```
$ ldd hello
	linux-vdso.so.1 (0x00007ffc48ffa000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f1cb8528000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f1cb875d000)
```
Included header file could be located at `/usr/include`.
