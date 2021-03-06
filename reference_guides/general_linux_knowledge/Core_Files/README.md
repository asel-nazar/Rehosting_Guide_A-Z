# Core Files

First and foremost, core files are created by the operating system (Linux in most cases for OpenFrame) rather than by the OpenFrame application itself. Core files can be maintained by a Linux Administrator. For more information on core files, it can be found here:

![Link to core file manual page](https://man7.org/linux/man-pages/man5/core.5.html)

For debugging purposes, it is important to provide Tmax with core files, and listing files so that any issues with the OpenFrame product can be resolved and patches can be delivered.

The majority of the core files dealing with OpenFrame will be generated in $OPENFRAME_HOME/core/appbin by default. You can find them with the following command.

```bash
ls $OPENFRAME_HOME/core/appbin/core.*

or

cd $OPENFRAME_HOME
find . -name core.*
```

# Interrogating a Core File

We first use the ```file``` command to see which binary file created the core file. Then we use the GNU Project Debugger (```gdb```) which allows you to see what happens in the call stack when a program aborts abnormally and creates a dump file.

## Step 1.)

Use the file command to determine which program created the core file.

```bash
cd $OPENFRAME_HOME/core/appbin
file core.xxxx
```

**Note:** xxxx denotes a number. Generally, core files are denoted by the name ```core.``` with some numbers

### Example Output

```bash
oframe@hostname /> file core.12345
core.12345: ELF 64-bit LSB core file x86-64, version 1 (SYSV), SVR4-style, from 'osibmpsv -- MBR=PROGRAM1 PSB=PROGPSB1 NBA=11 OBA=9 IMSID=REG1 AGN=ABCD0001',real uid:22524 1, effective uid: 225241, real gid: 29, effective gid: 29, execfn: '/u01_t0/tmaxapp/OpenFrame/core/appbin/osibmpsv', platform: 'x86_64'
```

We can see in the Example Output that the executed function (execfn) is /u01_t0/tmaxapp/OpenFrame/core/appbin/osibmpsv

## Step 2.) 

Next, we use the GNU Project Debugger (```gdb```) from the /usr/bin directory. Pass the binary found in Step 1 as well as the core file. The general usage is like so:

### General Usage

```bash
/usr/bin/gdb <execfn output from step 2> <core.xxxx>
```

### Example Usage

```bash
/usr/bin/gdb /u01_t0/tmaxapp/OpenFrame/core//appbin/osibmpsv core.12345
```

### Example Output

```bash
oframe@hostname /> /usr/bin/gdb /u01_t0/tmaxapp/OpenFrame/core//appbin/osibmpsv core.12345
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-115.e17
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law. Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-redhat-linux-gnu".
For bug reportting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>...
Reading symbols from /u01_t0/tmaxapp/OpenFrame/core/appbin/osibmpsv.7_1_0_56.212425...done.

warning: core file may not match specified executable file.
(New LWP 12345)
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
Core was generated by `osibmpsv -- MBR=PROGRAM1 PSB=PROGPSB1 NBA=11 OBA=9 IMSID=REG1 AGN=ABCD0001'.
Program terminated with signal 6, Aborted.
#0 0x00007fab43a8237 in raise () from /lib64/libc.so.6
Missing separate debuginfos, use: debuginfo-install glibc-2.17-292.e17.x86_64 libgcc-4.8.5-39.e17.x86_64 libstdc++-4.8.5-39.e17.x86_64 nss-softokn-freebl-3.44.0-5.e17.x86_64
(gdb)
```

## Step 3.)

Use the backtrace command inside gdb to show the call stack

```
bt
```

### Example Output

```bash
oframe@hostname /> /usr/bin/gdb /u01_t0/tmaxapp/OpenFrame/core//appbin/osibmpsv core.12345
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-115.e17
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law. Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-redhat-linux-gnu".
For bug reportting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>...
Reading symbols from /u01_t0/tmaxapp/OpenFrame/core/appbin/osibmpsv.7_1_0_56.212425...done.

warning: core file may not match specified executable file.
(New LWP 12345)
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
Core was generated by `osibmpsv -- MBR=PROGRAM1 PSB=PROGPSB1 NBA=11 OBA=9 IMSID=REG1 AGN=ABCD0001'.
Program terminated with signal 6, Aborted.
#0 0x00007fab43a82377 in raise () from /lib64/libc.so.6
Missing separate debuginfos, use: debuginfo-install glibc-2.17-292.e17.x86_64 libgcc-4.8.5-39.e17.x86_64 libstdc++-4.8.5-39.e17.x86_64 nss-softokn-freebl-3.44.0-5.e17.x86_64
(gdb) bt
#0   0x00007fab43a82377 in raise () from /lib64/libc.so.6
#1   0x00007fab4383a687 in abourt () from /lib64/libc.so.6
#2   0x00007fab4387b196 in __assert_fail_base () from /lib64/libc.so.6
#3   0x00007fab43a7b424 in __assert_fail () from /lib64/libc.so.6
#4   0x00007fab4387b242 in ofcob_assert () from /u01_t0/tmaxapp/OFCOBOL/lib/libofcobRTL.so
#5   0x00007fab286a7d71 in PROGRAM1 () at ofcbpp_PROGRAM1:5408
#6   0x0000000000dffe00 in ?? ()
#7   0x0000000000e7dcc0 in ?? ()
#8   0x0000000000de1800 in ?? ()
#9   0x0000000000f2a9c0 in ?? ()
#10  0x00007fab286a8f26 in PROGRAM1 () at ofcbpp_PROGRAM1:5445
#11  0x00007fab422c1429 in ?? () from /u01_t0/tmaxapp/OpenFrame/lib/libdsalc.so
#12  0x0000000100000000 in ?? ()
#13  0x00007fab477c9502 in _dl_map_object () from /lib64/ld-linux-x86-64.so.2
#14  0x00007fab467b5000 in ?? ()
#15  0x0000000000000000 in ?? ()
```

## Step 4.)

Next we are going to bring up the source viewing screen. To do this from the screenshot above, you need to type the following:

```bash
<CTRL> + x + a           (Hold Control, Then Type "x" then "a")
```

You can type ```up``` to go up in the stack.

Continue to type ```up``` until you find a spot in the source where the error occurred. In this specific example, there was an example in the ACCEPT statement.

**Screenshot ommitted**

## Step 5.) Next, we need to get information about the source file, so that you can attach it and send it to the appropriate party.

```bash
info source
```

### Example Output

```bash
(gdb) info source
Current source file is ofcbpp_PROGRAM1
Compilation directory is /u01_t2/oframe/jwhan/JOB/JOBNAME1
Located in /u01_t2/oframe/jwhan/JOB/JOBNAME1/ofcbpp_PROGRAM1
Contains 5492 lines. 
Source language is minimal
Compiled with DWARF 2 debugging format.
Does not include preprocessor macro info.
(gdb)
```

You can see in this example, the location of the file is in /u01_t2/oframe/jwhan/JOB/JOBNAME1/ofcbpp_PROGRAM1

## Step 6.) 

Lastly, you should attach the following and send to the appropriate party:

1. The core file itself.
2. The file found in step 5.