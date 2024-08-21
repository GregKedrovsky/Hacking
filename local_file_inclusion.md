# Local File Inclusion (LFI)

**[Local File Inclusion[(https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion)**: The File Inclusion vulnerability allows an attacker to include a file, usually exploiting a “dynamic file inclusion” mechanisms implemented in the target application. The vulnerability occurs due to the use of user-supplied input without proper validation.

This can lead to something as outputting the contents of the file, but depending on the severity, it can also lead to:
- Code execution on the web server
- Code execution on the client-side such as JavaScript which can lead to other attacks such as cross site scripting (XSS)
- Denial of Service (DoS)
- Sensitive Information Disclosure

LFI is the process of including files, ***that are already locally present on the server***, through the exploiting of vulnerable inclusion procedures implemented in the application.

Easy test for LFI (change the syntas per your target): `http://[target_ip]/?file=../../../../../etc/passwd` 
- Same result with curl: `curl 'http://[target_ip]/?file=/etc/passwd`
- If you get the file contents, it's vulnerable.


