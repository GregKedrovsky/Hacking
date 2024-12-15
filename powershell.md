# PowerShell Quick Reference

## Resources:
- Intewrnal All the Things: [Powershell Cheatsheet](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/powershell-cheatsheet/)

## [PowerShell Equivalent of Find](https://jessitron.com/2020/04/23/powershell-equivalent-of-find/)
```
gci -r -fi <filename-pattern>
```
- `gci` : short for `Get-ChildItem`
- `-r` : short for `-Recurse`; says, go down all the directories.
- `-fi <string>` : short for `-Filter <string>`; selects by name. This supports `*` and `?` wildcards, NOT regex.
- Equivalents: 
```
bash# find . -name '*.jar'
pwsh> Get-ChildItem -Recurse -Filter '*.jar'
pwsh> gci -r -fi *.jar
```
