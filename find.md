# Find Stuff in Linux

There are four basic commands in Linux to find stuff: 
- [whereis]()
- [which]()
- [locate]()
- [find]()

## whereis
> Searches for binary files, source files, and man pages for the specified command names.

### Switches:
- `-b` : searches for binaries
- `-s` : searches for source files
- `-m` :  searches for manuals

### Examples: 
```
whereis ls     # lists binaries, source and manuals if they exist
whereis -b ls  # lists only the binaries
whereis -s ls  # lists only the source files
whereis -m ls  # lists only the man files, if they exist
```

## which


## locate


## find
