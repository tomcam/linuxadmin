# Linux admin cheatsheet

This is just meant for me because I don't know where to find a summary like this
anywhere else. If you know a better cheatsheet or have some suggestions, feel 
free to contact me at tomcampbell@gmail.com.

### CentOS
[Multiple command shells](#shells)

## Maintenance on CentOS

### View installed packages with yum list

See what yum packages are installed.
It will probably be a long list.

```
# Get a list of all installed yum packages.
sudo yum list
```

You will probably have hundreds or thousands of packages installed.
The first few lines of the output will look something like this:

```
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
Installed Packages
GeoIP.x86_64                   1.5.0-11.amzn2.0.2    installed 
PyYAML.x86_64                  3.10-11.amzn2.0.2     installed 
acl.x86_64                     2.2.51-14.amzn2       installed 
acpid.x86_64                   2.0.19-9.amzn2.0.1    installed 
amazon-linux-extras.noarch     1.6.7-1.amzn2         installed 
amazon-ssm-agent.x86_64        2.3.372.0-1.amzn2     installed 
at.x86_64                      3.1.13-23.amzn2       installed 
attr.x86_64                    2.4.46-12.amzn2.0.2   installed 
...etc
```


### Search for a particular package with yum list installed and grep

Suppose you want to know if `nvim` is installed.
You can use grep:

```
# Get a list of installed packages, pipe it
# through grep, and see if `nano` is installed.
sudo yum list installed | grep nano
```

### Determine if a yum package exists with yum list

Suppose you want to know if a yum package named `nvim` is
available at all. Just append it to `yum list`:

```
# Find out if there's a yum package for
# the  editor.
sudo yum list nano
```

If it's available, you'll see something like this:

```
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
Installed Packages
nano.x86_64      2.9.8-2.amzn2.0.1    installed
```

<a id="update"></a>
### Daily/every few days maintenance on CentOS: yum update

Every day or three use `sudo yum update` to see what
updates are necessary. 
Depending on what version of Linux you use,
`update` leaves older versions
of the packages on your system. See note below.

```
# See what updates are available.
sudo yum check-update
# Update all changed packages by
# installing patches and updates.
sudo yum update
```

### Note: yum update may also delete obsolete packages

Since most admins prefer to delete unneeded packages after
running `yum update`, many versions of Linux now
default to doing so. 
Examples include Amazon Linux 2, Fedora, RHEL, and CentOS.

You can tell what behavior your version has by examining
the file `/etc/yum.conf`. If it contains the line `obsoletes=1`
then `yum update` also deletes the outdated packages:


```bash
grep "obsoletes" /etc/yum.conf
```

If you see `obsoletes=1` then it means running `yum update`
is actually the same as running `yum update --obsoletes`,
which removes the old files.

```
obsoletes=1
```


<a id="upgrade"></a>
### Like update but deleting old packages on CentOS: yum upgrade

`update` ensures you have current versions of packages on your system.
It leaves the old ones intact. If you want the same updates but 
prefer to delete the obsolete packages, use `yum upgrade`:

```
# Update all changed packages by
# installing patches and updates.
# Delete outdated versions.
sudo yum upgrade
```

This is functionally equivalent to running `update --obsoletes`:
```
# The following two commands do the same thing.
# Update all changed packages by
# installing patches and updates.
# Delete outdated versions.
sudo yum upgrade
# --obsoletes means delete unneeded packages
sudo yum update --obsoletes
```

## Diagnostics and performance

### View memory usage and performance with top

You can get a live view of memory consumption, processor usages, running processes,
and many other objects owned by the operating system with `tops`.
It's incredibly flexible and configurable.

```
top
```

You'll get a display that looks something like this.

```
top - 00:42:42 up 526 days, 20:50,  3 users,  load average: 0.41, 0.11, 0.03
Tasks: 123 total,   1 running, 121 sleeping,   0 stopped,   1 zombie
Cpu(s):  0.0%us,  0.2%sy,  0.0%ni, 99.8%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Mem:  32969820k total, 32907372k used,    62448k free,   602692k buffers
Swap: 33554424k total,    52712k used, 33501712k free, 30891660k cached

  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                         
10005 postgres  15   0 8430m 108m 101m S    1  0.3   0:00.13 postgres                         
    1 root      15   0  3784  620  524 S    0  0.0   0:01.20 init                             
    2 root      RT   0     0    0    0 S    0  0.0   0:04.53 migration/0                      
    3 root      34  19     0    0    0 S    0  0.0   0:00.41 ksoftirqd/0                      
    4 root      RT   0     0    0    0 S    0  0.0   0:00.00 watchdog/0                       
    5 root      RT   0     0    0    0 S    0  0.0   0:04.50 migration/1                      
    6 root      34  19     0    0    0 S    0  0.0   0:00.07 ksoftirqd/1                      
    7 root      RT   0     0    0    0 S    0  0.0   0:00.00 watchdog/1                       
    8 root      RT   0     0    0    0 S    0  0.0   0:05.63 migration/2                      
    9 root      34  19     0    0    0 S    0  0.0   0:00.06 ksoftirqd/2                      
   10 root      RT   0     0    0    0 S    0  0.0   0:00.00 watchdog/2                       
   11 root      RT   0     0    0    0 S    0  0.0   0:04.32 migration/3                      
   12 root      34  19     0    0    0 S    0  0.0   0:00.04 ksoftirqd/3           
   ```

`top` has dozens of commands you issue by pressing a single key, and
case matters.

* To quit `top`, press `q`.
* To learn more about `top`, press `h`.
* To show output in color (or not) press `z`.
* To decide what colors to use, press `Z`.

### Example top configuration: Sort by %MEM 

Read through the options when you get a spare moment. You'll find
`top` is very, very easy to reconfigure quickly. 

Suppose you want to see `%MEM` in the leftmost column for clarity? 
Press `h` and you'll see a cheatsheet:

```
Help for Interactive Commands - procps version 3.2.7
Window 1:Def: Cumulative mode Off.  System: Delay 3.0 secs; Secure mode Off.

  Z,B       Global: 'Z' change color mappings; 'B' disable/enable bold
  l,t,m     Toggle Summaries: 'l' load avg; 't' task/cpu stats; 'm' mem info
  1,I       Toggle SMP view: '1' single/separate states; 'I' Irix/Solaris mode

  f,o     . Fields/Columns: 'f' add or remove; 'o' change display order
  F or O  . Select sort field
  <,>     . Move sort field: '<' next col left; '>' next col right
  R,H     . Toggle: 'R' normal/reverse sort; 'H' show threads
  c,i,S   . Toggle: 'c' cmd name/line; 'i' idle tasks; 'S' cumulative time
  x,y     . Toggle highlights: 'x' sort field; 'y' running tasks
  z,b     . Toggle: 'z' color/mono; 'b' bold/reverse (only if 'x' or 'y')
  u       . Show specific user only
  n or #  . Set maximum tasks displayed

  k,r       Manipulate tasks: 'k' kill; 'r' renice
  d or s    Set update interval
  W         Write configuration file
  q         Quit
          ( commands shown with '.' require a visible task display window ) 
Press 'h' or '?' for help with Windows,
any other key to continue 
```

As you can see you'd press uppercase `O` to choose the sort column, and this screen appears:

```
Current Sort Field:  k  for window 1:Def
Select sort field via field letter, type any other key to return 

  a: PID        = Process Id
  b: PPID       = Parent Process Pid
  c: RUSER      = Real user name
  d: UID        = User Id
  e: USER       = User Name
  f: GROUP      = Group Name
  g: TTY        = Controlling Tty
  h: PR         = Priority
  i: NI         = Nice value
  j: P          = Last used cpu (SMP)
* k: %CPU       = CPU usage
  l: TIME       = CPU Time
  m: TIME+      = CPU Time, hundredths
  N: %MEM       = Memory usage (RES)
  o: VIRT       = Virtual Image (kb)
  p: SWAP       = Swapped size (kb)
  q: RES        = Resident size (kb)
  r: CODE       = Code size (kb)
  s: DATA       = Data+Stack size (kb)
  t: SHR        = Shared Mem size (kb)
  u: nFLT       = Page Fault count
  v: nDRT       = Dirty Pages count
  w: S          = Process Status
  x: COMMAND    = Command name/line
  y: WCHAN      = Sleeping in Function
  z: Flags      = Task Flags <sched.h>

Note1:
  If a selected sort field can't be
  shown due to screen width or your
  field order, the '<' and '>' keys
  will be unavailable until a field
  within viewable range is chosen.

Note2:
  Field sorting uses internal values,
  not those in column display.  Thus,
  the TTY & WCHAN fields will violate
  strict ASCII collating sequence.
  (shame on you if WCHAN is chosen)
```

As you can see, the sort order is by CPU usage. You'd choose `N` to sort by `%MEM` 
and the display would update:

```
Current Sort Field:  N  for window 1:Def
Select sort field via field letter, type any other key to return 

  a: PID        = Process Id
  b: PPID       = Parent Process Pid
  c: RUSER      = Real user name
  d: UID        = User Id
  e: USER       = User Name
  f: GROUP      = Group Name
  g: TTY        = Controlling Tty
  h: PR         = Priority
  i: NI         = Nice value
  j: P          = Last used cpu (SMP)
  k: %CPU       = CPU usage
  l: TIME       = CPU Time
  m: TIME+      = CPU Time, hundredths
* N: %MEM       = Memory usage (RES)
```

Press any key to continue and you'll see that `top` now sorts by memory usage:

```
  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                         
10593 postgres  15   0 8428m 7.4g 7.4g S    0 23.5  47:18.78 postgres                         
11912 postgres  15   0 8431m 1.2g 1.2g S    0  3.9   0:22.13 postgres                         
23368 postgres  15   0 8429m 942m 936m S    0  2.9   0:14.36 postgres                         
11927 postgres  15   0 8429m 737m 730m S    0  2.3   0:10.96 postgres                         
11924 postgres  15   0 8429m 705m 699m S    0  2.2   0:01.64 postgres                         
10594 postgres  15   0 8427m 451m 447m S    0  1.4   0:01.43 postgres                         
28454 postgres  15   0 8429m 385m 378m S    0  1.2   0:00.95 postgres                         
11911 postgres  15   0 8430m 307m 300m S    0  1.0   0:28.32 postgres        
```

## Disk layout

### Individual drives and virtual drives

Linux is installed in a directory tree with `/` (forward slash, not backslash as in Windows.

**Subdirectories** are given names like `/dev` and `/usr`. 

**Disk drives** are given designations starting with `sd` followed by a letter. They liv in the `/dev` directory.
The first drive name defaults to `sda`, the next drive is named `sdb`, and so on. So you'd address them
as `/dev/sda`, `/dev/sdb`, etc.

**Disk partitions** are given numbers appended to the drive designation, 
so partition 1 on drive `/dev/sda` is addressed as `/dev/sda1`, partition 2 is addressed
as `/dev/sda2`, and so on.

## Permissions

### sudo and su

`sudo -s` (followed by a password) keeps you logged in as sudo.

## Command shell

<a id="shells"></a>
### Multiple command shells

The key sequence `Alt+Right` (`⌘+Right` on MacOS) switches you to the next terminal, 
and of course `Alt+Left`/`⌘+Left` to the previous session.
