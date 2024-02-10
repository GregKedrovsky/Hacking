# Social Engineering

> Tools and techniques to hack humans.

## [SEToolkit](https://github.com/trustedsec/social-engineer-toolkit)
> The Social-Engineer Toolkit (SET) is an open-source penetration testing framework designed for social engineering. SET has a number of custom attack vectors that allow you to make a believable attack quickly.

### Watering Hole

A simple example: Lure people into a fake Facebook page, harvest their login creds.
- The following will capture all POSTs on a website.
- It's best if the cloned page has a username and password form.
- But, regardless… you'll get all POSTs from the cloned page.

### Steps
- Start SET
- Select 1 from the menu: Social-Engineering Attacks
- Select 2 from the menu: Website Attack Vectors
- Select 3 from the menu: Credential Harvester Attack Method
- Select 2 from the menu: Site Cloner (POST back IP address is the address to which your harvested creds will be sent; it defaults to your current IP; leave it alone unless you have another IP to use.)
- Enter the URL you want to clone: `https://www.facebook.com/`

The target machine needs to enter your attack box IP into their browser… the cloned URL will load… target enters creds… and they register in SET.
