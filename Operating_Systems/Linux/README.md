# Linux

## General

### Filesystem

Filesystem Hierarchy Standard - Wikipedia
https://en.m.wikipedia.org/wiki/Filesystem_Hierarchy_Standard



### Cache

If you want to flush the file system cache(s) then have a look at this blog post:

https://www.tecmint.com/clear-ram-memory-cache-buffer-and-swap-space-on-linux/

This command needs to be run as root and is "safe"as it only clears the PageCache (not dentries and inodes):

```
sync; echo 1 > /proc/sys/vm/drop_caches
```





## Tips

### set -ex

Anyone who sees it for the first time might wonder why `set -ex` appears in scripts.

[set -ex - The most useful bash trick of the year](https://www.peterbe.com/plog/set-ex)



## Security

### Hardening

Three Critical Resources You Should Use to Harden Your Linux Server - Make Tech Easier
https://www.google.com/amp/s/www.maketecheasier.com/resources-harden-linux-server/%3famp

CIS Benchmarks Landing Page
https://learn.cisecurity.org/benchmarks

Lynis - Security auditing tool for Linux, macOS, and Unix-based systems - CISOfy
https://cisofy.com/lynis/#operating-systems

