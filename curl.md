# curl 

A command-line tool to transfer data to or from a server.

Download the home/index page from a site: 

```
curl http://[target ip]
```

Upload a file using PUT: 

```
curl http://[target ip/[subdir] --upload-file gk_put.txt
# or
curl -X PUT -T "/path/to/file" "http://myputserver.com/puturl.tmp"
```

**Note:** `-X PUT` is redundant when using `-T` (which is short for `--upload-file`)
