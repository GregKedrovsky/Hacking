# Strings

The Linux [strings](https://www.howtogeek.com/427805/how-to-use-the-strings-command-on-linux/) command ([man page](https://linux.die.net/man/1/strings)).
1. `strings` can be used to work out what type of a file you are looking at.
2. `strings` can be used to display the human-readable text.
- The swap file will have a lot of human-unreadable content.
-  It will display the human-readable character of any file (like comments in a compiled binary).

Example Syntax: 
```
strings login.php.swp
```
