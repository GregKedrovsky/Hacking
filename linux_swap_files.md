# Swap Files
> Files ending in `.swp` are swap files

## What is a Swap File?

A swap (.swp) file store content for the specific file — for example, while you’re editing a file with vim. They are set up when you start an edit session and then automatically removed when you’re done unless some problem occurs and your editing session doesn’t complete properly. In that case, vim will offer you a chance to recover your work where you left off. [Source](https://www.networkworld.com/article/939724/what-are-unix-swap-swp-files.html).
- If the file being edited were called chkAccts.sh, for example, the swap file set up when you begin your edit would be called `.chkAccts.sh.swp`.
- You could spot it sitting in the same directory were you to look for it from another window or login session.
- More information on swap files can be found [here](https://www.techtarget.com/searchwindowsserver/definition/swap-file-swap-space-or-pagefile).

## Examining Swap Files

If you happen upon a vim swap file (e.g., login.php.swp) and want to see what's inside, here are two ways.
1. If it's a Vim swap file, open it with vim: `vim -r [swap filename]`. If that doesn't work...
2. Use the Linux [strings](https://www.howtogeek.com/427805/how-to-use-the-strings-command-on-linux/) command ([man page](https://linux.die.net/man/1/strings)).
- The swap file will have a lot of human-unreadable content.
- `strings` will only display the human-readable text. It will display the human-readable character of any file (like comments in a compiled binary).
```
strings login.php.swp
```


