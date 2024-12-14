# [GTFOBins](https://gtfobins.github.io/)
> Get The F**k Out Binaries

GTFOBins is a curated list of Unix binaries that can be used to bypass local security restrictions in misconfigured systems.
- The project collects legitimate functions of Unix binaries that can be abused to get the f**k (break) out restricted shells, escalate or maintain elevated privileges, transfer files, spawn bind and reverse shells, and facilitate the other post-exploitation tasks.
- It is important to note that this is not a list of exploits, and the programs listed here are not vulnerable per se, rather, GTFOBins is a compendium about how to live off the land when you only have certain binaries available.

## [find](https://gtfobins.github.io/gtfobins/find/)

If the binary is allowed to run as superuser by sudo, it does not drop the elevated privileges and may be used to access the file system, escalate or maintain privileged access.
- The `-exec` flag is used with `find` to run some command on each matching file that is found.
- Since the process is running as root (from sudo ), that shell will also be running as root. Instead of `sh`, you can use `bash` as it offers better syntax and it is actually an improvement over `sh`.
```
sudo find . -exec /bin/sh \; -quit
# or
sudo find . -exec /bin/bash \; -quit
```