# Enumeration: SMTP
> Simple Mail Transfer Protocol (SMTP)

- Default SMTP Port: `25`

## Brute-Force Usernames Via SMTP

- MSF Module: `smtp_enum`
- use... info... set options...
  - NOTE: If your target is Linux, set the `UNIXONLY` option to **true**
- RESULTS: Analyze that list of usernames...
  - Service accounts (e.g., back-up, bin, ftp, games, mysql, sshd, etc.) will not have passwords.
  - However, you should try to pick out "service" names that don't quite look like real services names (e.g., service).
  - Also, pick out the actual usernames that could be login names (e.g., user, etc.).
  - Make a csv text file with the usernames you picked out and use it in [Hydra](../hydra.md) to brute-force a login.
