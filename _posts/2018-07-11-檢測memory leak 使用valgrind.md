---
layout: post
title: 檢測memory leak 使用 valgrind
date: 2018-07-11
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: https://lh3.googleusercontent.com/fesu-l5g6yAEiWxbsD-4TiXo6rYJ68r7u7MsolVwnnfgnLDC7h0uAc8lF74ZSbZpvTd_lY3A4iYAnAy7VzxAp_fKxQrwiyALqUOHmlC8miKg9lXDlGDvPQuZv9QWTAyUtttyZLHGDw=w2400
category: programming
tags: memory_leak candcpp cmd

---

# 關於valgrind
參考：https://zh.wikipedia.org/wiki/Valgrind

------
# 五種memory leak說明

- "definitely lost" 
means your program is leaking memory -- fix those leaks!


- "indirectly lost" 
means your program is leaking memory in a pointer-based structure. (E.g. if the root node of a binary tree is "definitely lost", all the children will be "indirectly lost".) If you fix the "definitely lost" leaks, the "indirectly lost" leaks should go away.


- "possibly lost" 
means your program is leaking memory, unless you're doing unusual things with pointers that could cause them to point into the middle of an allocated block; see the user manual for some possible causes. Use --show-possibly-lost=no if you don't want to see these reports.


- "still reachable" 
means your program is probably ok -- it didn't free some memory it could have. This is quite common and often reasonable. Don't use --show-reachable=yes if you don't want to see these reports.


- "suppressed" 
means that a leak error has been suppressed. There are some suppressions in the default suppression files. You can ignore suppressed errors.

------

# valgrind安裝
嘗試過現在已經無法在新版本的OSX上跑了
所幸這陣子學到如何使用docker
立馬用先前寫好的dockerfile啟動一個ubuntu環境
``` python
sudo apt-get install valgrind
```

以最近執行的專案為例
先將專案compile
``` python
gcc -g -o Output main.c graph.c Node.c tree.c forest.c 
graph_generator.c Array.c ./rngs/rngs.c -lm
```


之後對輸出的Output檔做檢測
``` python
valgrind --leak-check=full --show-leak-kinds=all 
--verbose ./Output
```

檢測結果範例：
``` c
==666== Memcheck, a memory error detector
==666== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==666== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info
==666== Command: ./Output
==666== 
--666-- Valgrind options:
--666--    --leak-check=full
--666--    --show-leak-kinds=all
--666--    --verbose
--666-- Contents of /proc/version:
--666--   Linux version 4.9.87-linuxkit-aufs (root@95fa5ec30613) (gcc version 6.4.0 (Alpine 6.4.0) ) #1 SMP Wed Mar 14 15:12:16 UTC 2018
--666-- 
--666-- Arch and hwcaps: AMD64, LittleEndian, amd64-cx16-lzcnt-sse3-avx-avx2-bmi
--666-- Page sizes: currently 4096, max supported 4096
--666-- Valgrind library directory: /usr/lib/valgrind
--666-- Reading syms from /usr/src/app/src/multype_mulbs/src/Output
--666-- Reading syms from /lib/x86_64-linux-gnu/ld-2.27.so
--666--   Considering /lib/x86_64-linux-gnu/ld-2.27.so ..
--666--   .. CRC mismatch (computed 1b7c895e wanted 2943108a)
--666--   Considering /usr/lib/debug/lib/x86_64-linux-gnu/ld-2.27.so ..
--666--   .. CRC is valid
--666-- Reading syms from /usr/lib/valgrind/memcheck-amd64-linux
--666--   Considering /usr/lib/valgrind/memcheck-amd64-linux ..
--666--   .. CRC mismatch (computed 62965bbf wanted eeb84137)
--666--    object doesn't have a symbol table
--666--    object doesn't have a dynamic symbol table
--666-- Scheduler: using generic scheduler lock implementation.
--666-- Reading suppressions file: /usr/lib/valgrind/default.supp
==666== embedded gdbserver: reading from /tmp/vgdb-pipe-from-vgdb-to-666-by-???-on-d2b0a3be48cf
==666== embedded gdbserver: writing to   /tmp/vgdb-pipe-to-vgdb-from-666-by-???-on-d2b0a3be48cf
==666== embedded gdbserver: shared mem   /tmp/vgdb-pipe-shared-mem-vgdb-666-by-???-on-d2b0a3be48cf
==666== 
==666== TO CONTROL THIS PROCESS USING vgdb (which you probably
==666== don't want to do, unless you know exactly what you're doing,
==666== or are doing some strange experiment):
==666==   /usr/lib/valgrind/../../bin/vgdb --pid=666 ...command...
==666== 
==666== TO DEBUG THIS PROCESS USING GDB: start GDB like this
==666==   /path/to/gdb ./Output
==666== and then give GDB the following command
==666==   target remote | /usr/lib/valgrind/../../bin/vgdb --pid=666
==666== --pid is optional if only one valgrind process is running
==666== 
--666-- REDIR: 0x401f2f0 (ld-linux-x86-64.so.2:strlen) redirected to 0x58060901 (???)
--666-- REDIR: 0x401f0d0 (ld-linux-x86-64.so.2:index) redirected to 0x5806091b (???)
--666-- Reading syms from /usr/lib/valgrind/vgpreload_core-amd64-linux.so
--666--   Considering /usr/lib/valgrind/vgpreload_core-amd64-linux.so ..
--666--   .. CRC mismatch (computed 13d5e98a wanted 1786ecf1)
--666--    object doesn't have a symbol table
--666-- Reading syms from /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so
--666--   Considering /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so ..
--666--   .. CRC mismatch (computed 8f77ef36 wanted fcbde737)
--666--    object doesn't have a symbol table
==666== WARNING: new redirection conflicts with existing -- ignoring it
--666--     old: 0x0401f2f0 (strlen              ) R-> (0000.0) 0x58060901 ???
--666--     new: 0x0401f2f0 (strlen              ) R-> (2007.0) 0x04c32db0 strlen
--666-- REDIR: 0x401d360 (ld-linux-x86-64.so.2:strcmp) redirected to 0x4c33ee0 (strcmp)
--666-- REDIR: 0x401f830 (ld-linux-x86-64.so.2:mempcpy) redirected to 0x4c374f0 (mempcpy)
--666-- Reading syms from /lib/x86_64-linux-gnu/libm-2.27.so
--666--   Considering /lib/x86_64-linux-gnu/libm-2.27.so ..
--666--   .. CRC mismatch (computed 7feae033 wanted b29b2508)
--666--   Considering /usr/lib/debug/lib/x86_64-linux-gnu/libm-2.27.so ..
--666--   .. CRC is valid
--666-- Reading syms from /lib/x86_64-linux-gnu/libc-2.27.so
--666--   Considering /lib/x86_64-linux-gnu/libc-2.27.so ..
--666--   .. CRC mismatch (computed b1c74187 wanted 042cc048)
--666--   Considering /usr/lib/debug/lib/x86_64-linux-gnu/libc-2.27.so ..
--666--   .. CRC is valid
--666-- REDIR: 0x5278c70 (libc.so.6:memmove) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5277d40 (libc.so.6:strncpy) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5278f50 (libc.so.6:strcasecmp) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5277790 (libc.so.6:strcat) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5277d70 (libc.so.6:rindex) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x527a7c0 (libc.so.6:rawmemchr) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5278de0 (libc.so.6:mempcpy) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5278c10 (libc.so.6:bcmp) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5277d00 (libc.so.6:strncmp) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5277800 (libc.so.6:strcmp) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5278d40 (libc.so.6:memset) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x52960f0 (libc.so.6:wcschr) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5277ca0 (libc.so.6:strnlen) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5277870 (libc.so.6:strcspn) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5278fa0 (libc.so.6:strncasecmp) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5277840 (libc.so.6:strcpy) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x52790e0 (libc.so.6:memcpy@@GLIBC_2.14) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5277da0 (libc.so.6:strpbrk) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x52777c0 (libc.so.6:index) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5277c70 (libc.so.6:strlen) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x52826c0 (libc.so.6:memrchr) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5278ff0 (libc.so.6:strcasecmp_l) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5278be0 (libc.so.6:memchr) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5296eb0 (libc.so.6:wcslen) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5278050 (libc.so.6:strspn) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5278f20 (libc.so.6:stpncpy) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5278ef0 (libc.so.6:stpcpy) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x527a7f0 (libc.so.6:strchrnul) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x5279040 (libc.so.6:strncasecmp_l) redirected to 0x4a2a6e0 (_vgnU_ifunc_wrapper)
--666-- REDIR: 0x53683c0 (libc.so.6:__strrchr_avx2) redirected to 0x4c32730 (rindex)
--666-- REDIR: 0x53681d0 (libc.so.6:__strchrnul_avx2) redirected to 0x4c37020 (strchrnul)
--666-- REDIR: 0x5368ad0 (libc.so.6:__memcpy_avx_unaligned_erms) redirected to 0x4c366e0 (memmove)
--666-- REDIR: 0x5368590 (libc.so.6:__strlen_avx2) redirected to 0x4c32cf0 (strlen)
--666-- REDIR: 0x5271070 (libc.so.6:malloc) redirected to 0x4c2faa0 (malloc)
--666-- REDIR: 0x5278590 (libc.so.6:__GI_strstr) redirected to 0x4c37760 (__strstr_sse2)
--666-- REDIR: 0x5368ab0 (libc.so.6:__mempcpy_avx_unaligned_erms) redirected to 0x4c37130 (mempcpy)
running graph = 0
--666-- REDIR: 0x5271950 (libc.so.6:free) redirected to 0x4c30cd0 (free)
graphRead: Error occur while openning file "./graph/Node250_region235/0.g" !
==666== 
==666== HEAP SUMMARY:
==666==     in use at exit: 1,053,376 bytes in 1,064 blocks
==666==   total heap usage: 1,066 allocs, 2 frees, 1,054,952 bytes allocated
==666== 
==666== Searching for pointers to 1,064 not-freed blocks
==666== Checked 92,280 bytes
==666== 
==666== 16 bytes in 1 blocks are still reachable in loss record 1 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E4E: Array_Create (Array.c:17)
==666==    by 0x108F9E: main (main.c:148)
==666== 
==666== 16 bytes in 1 blocks are still reachable in loss record 2 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E4E: Array_Create (Array.c:17)
==666==    by 0x108FB2: main (main.c:149)
==666== 
==666== 16 bytes in 1 blocks are still reachable in loss record 3 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E4E: Array_Create (Array.c:17)
==666==    by 0x108FFC: main (main.c:152)
==666== 
==666== 16 bytes in 1 blocks are still reachable in loss record 4 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E4E: Array_Create (Array.c:17)
==666==    by 0x109010: main (main.c:153)
==666== 
==666== 16 bytes in 1 blocks are still reachable in loss record 5 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E4E: Array_Create (Array.c:17)
==666==    by 0x10909F: main (main.c:163)
==666== 
==666== 32 bytes in 1 blocks are still reachable in loss record 6 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x109190: main (main.c:178)
==666== 
==666== 32 bytes in 1 blocks are still reachable in loss record 7 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x1091B9: main (main.c:182)
==666== 
==666== 64 bytes in 1 blocks are still reachable in loss record 8 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x108F79: main (main.c:146)
==666== 
==666== 64 bytes in 1 blocks are still reachable in loss record 9 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x108F8A: main (main.c:147)
==666== 
==666== 64 bytes in 1 blocks are still reachable in loss record 10 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x109021: main (main.c:155)
==666== 
==666== 64 bytes in 1 blocks are still reachable in loss record 11 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x109032: main (main.c:156)
==666== 
==666== 64 bytes in 1 blocks are still reachable in loss record 12 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x109043: main (main.c:158)
==666== 
==666== 64 bytes in 1 blocks are still reachable in loss record 13 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x109200: main (main.c:187)
==666== 
==666== 64 bytes in 1 blocks are still reachable in loss record 14 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x109260: main (main.c:189)
==666== 
==666== 128 bytes in 8 blocks are still reachable in loss record 15 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E4E: Array_Create (Array.c:17)
==666==    by 0x10907F: main (main.c:161)
==666== 
==666== 552 bytes in 1 blocks are still reachable in loss record 16 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x5258E49: __fopen_internal (iofopen.c:65)
==666==    by 0x5258E49: fopen@@GLIBC_2.2.5 (iofopen.c:89)
==666==    by 0x108E81: main (main.c:132)
==666== 
==666== 552 bytes in 1 blocks are still reachable in loss record 17 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x5258E49: __fopen_internal (iofopen.c:65)
==666==    by 0x5258E49: fopen@@GLIBC_2.2.5 (iofopen.c:89)
==666==    by 0x108E9E: main (main.c:133)
==666== 
==666== 552 bytes in 1 blocks are still reachable in loss record 18 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x5258E49: __fopen_internal (iofopen.c:65)
==666==    by 0x5258E49: fopen@@GLIBC_2.2.5 (iofopen.c:89)
==666==    by 0x108EF3: main (main.c:137)
==666== 
==666== 1,000 bytes in 1 blocks are still reachable in loss record 19 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x108F29: main (main.c:142)
==666== 
==666== 1,000 bytes in 1 blocks are still reachable in loss record 20 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E71: Array_Create (Array.c:19)
==666==    by 0x108F9E: main (main.c:148)
==666== 
==666== 1,000 bytes in 1 blocks are still reachable in loss record 21 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E71: Array_Create (Array.c:19)
==666==    by 0x108FB2: main (main.c:149)
==666== 
==666== 1,000 bytes in 1 blocks are still reachable in loss record 22 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E71: Array_Create (Array.c:19)
==666==    by 0x108FFC: main (main.c:152)
==666== 
==666== 1,000 bytes in 1 blocks are still reachable in loss record 23 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E71: Array_Create (Array.c:19)
==666==    by 0x109010: main (main.c:153)
==666== 
==666== 1,000 bytes in 1 blocks are still reachable in loss record 24 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E71: Array_Create (Array.c:19)
==666==    by 0x10909F: main (main.c:163)
==666== 
==666== 1,000 bytes in 1 blocks are still reachable in loss record 25 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x10917F: main (main.c:173)
==666== 
==666== 2,000 bytes in 1 blocks are still reachable in loss record 26 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x108F0E: main (main.c:141)
==666== 
==666== 2,000 bytes in 1 blocks are still reachable in loss record 27 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x108F68: main (main.c:144)
==666== 
==666== 2,000 bytes in 1 blocks are still reachable in loss record 28 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x108FCD: main (main.c:150)
==666== 
==666== 2,000 bytes in 1 blocks are still reachable in loss record 29 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x108FE8: main (main.c:151)
==666== 
==666== 2,000 bytes in 1 blocks are still reachable in loss record 30 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x1091D4: main (main.c:184)
==666== 
==666== 2,000 bytes in 1 blocks are still reachable in loss record 31 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x1091EF: main (main.c:185)
==666== 
==666== 4,000 bytes in 250 blocks are still reachable in loss record 32 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E4E: Array_Create (Array.c:17)
==666==    by 0x1090FF: main (main.c:168)
==666== 
==666== 4,000 bytes in 250 blocks are still reachable in loss record 33 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E4E: Array_Create (Array.c:17)
==666==    by 0x10912B: main (main.c:169)
==666== 
==666== 8,000 bytes in 8 blocks are still reachable in loss record 34 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E71: Array_Create (Array.c:19)
==666==    by 0x10907F: main (main.c:161)
==666== 
==666== 8,000 bytes in 8 blocks are still reachable in loss record 35 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x109243: main (main.c:188)
==666== 
==666== 8,000 bytes in 8 blocks are still reachable in loss record 36 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x1092A3: main (main.c:190)
==666== 
==666== 250,000 bytes in 1 blocks are still reachable in loss record 37 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x108F4D: main (main.c:143)
==666== 
==666== 250,000 bytes in 1 blocks are still reachable in loss record 38 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x109164: main (main.c:172)
==666== 
==666== 250,000 bytes in 250 blocks are still reachable in loss record 39 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E71: Array_Create (Array.c:19)
==666==    by 0x1090FF: main (main.c:168)
==666== 
==666== 250,000 bytes in 250 blocks are still reachable in loss record 40 of 40
==666==    at 0x4C2FB0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==666==    by 0x116E71: Array_Create (Array.c:19)
==666==    by 0x10912B: main (main.c:169)
==666== 
==666== LEAK SUMMARY:
==666==    definitely lost: 0 bytes in 0 blocks
==666==    indirectly lost: 0 bytes in 0 blocks
==666==      possibly lost: 0 bytes in 0 blocks
==666==    still reachable: 1,053,376 bytes in 1,064 blocks
==666==         suppressed: 0 bytes in 0 blocks
==666== 
==666== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
==666== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

