# Swap Files
> Files ending in `.swp` are swap files

A swap (.swp) file store content for the specific file — for example, while you’re editing a file with vim. They are set up when you start an edit session and then automatically removed when you’re done unless some problem occurs and your editing session doesn’t complete properly. In that case, vim will offer you a chance to recover your work where you left off. [Source](https://www.networkworld.com/article/939724/what-are-unix-swap-swp-files.html).
- If the file being edited were called chkAccts.sh, for example, the swap file set up when you begin your edit would be called `.chkAccts.sh.swp`.
- You could spot it sitting in the same directory were you to look for it from another window or login session.
