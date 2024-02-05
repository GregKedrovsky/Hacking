# grep

grep for a “string” in all the files from root (/) or whatever directory you want: 

```
grep -rnw / -e "admin@foo.com" 2>/dev/null
grep -rnw /usr/share/ -e "admin@foo.com" 2>/dev/null
```

**Options:**  
- `-r` : recursive; read all files under each directory, recursively  
- `-n` : number; Prefix each line of output with the 1-based line number within its input file  
- `-w` : whole words; select only those lines containing matches that form whole words  
- `-e [pattern]` : expression; search for all patterns given; protect pattern with “quotes”  
- `2>/dev/null` : sends STDERR to the abyss instead of to the screen  
