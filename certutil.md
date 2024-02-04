Certutil is a command-line utility in a Windows OS that lets you manage and manipulate certificates and certificate services.

It can also be used on a Windows machine to transfer files, much like **wget** in Linux.

```
certutil -urlcache -f http://[target ip]/filename.exe filename.exe
```

The `-urlcache` is used to display or delete URL cache entries.  
The `-f` switch forces a fetch of the specified URL and then updates the cache

---

