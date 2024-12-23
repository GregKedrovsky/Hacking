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
> Returns the path to the executable indicated.

Example: If you want to know which version of Python is called when you type in “python” at the command line…
```
which python
```

## locate
> Fast way to search for files by name on your disk.

### Update Database
- Important: Update the database before first locate and after significant changes on your disk.
```
updatedb  # as root
```

`locate` will match the pattern you give it. So you may need to pipe to `grep` or provide a more specific search pattern. Example:
```
locate python  # locates all file names that include 'python'
```

## find
> The big daddy of all search functions in Linux...

### Basic Syntax


### Search Location Shortcuts


### Expressions

#### Most Common Expressions: 


#### Find Files by Type


#### Find Files by Size


#### Find Files by Owner


#### Find Files by Perms


#### Find Files by Timestamps

