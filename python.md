# Python Web Server

Setting up a Web Server with Python: 
- Python comes with a built-in modules for spinning up a simple web server. 
- These modules can be used to facilitate a simple HTTP server that gives you standard GET and HEAD request handlers.
- These modules can be used to host files in any directory of your system.
- The modules can be implemented through a single command on your terminal.

## Python 2:
```
python -m SimpleHTTPServer 80
```
- `-m` : module
- `80` : port (you can use any that are available)

## Python 3: 
```
python3 -m http.server 80
```
- `-m` : module
- `80` : port (you can use any that are available)

## Bash Shell via Python

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```
