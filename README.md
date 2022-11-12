# OWASP ModSecurity Core Rule Set - IP-Management Plugin

The OWASP Core Rule Set (CRS) comes with a plugin structure that allows
to add official and third-party plugins to work with the existing
baseline CRS installation.

This repository contains an IP-Management Plugin that provides:
 * Dynamic IP Tracking lists
 * TODO: Dynamic IP allow listing
 * TODO: Dynamic IP deny listing

The CRS plugin documentation can be found on the [website](https://coreruleset.org/docs/configuring/plugins/).

## How do CRS plugins work?

Plugins are rules like ordinary rules and they integrate with an existing
CRS installation. Starting with CRS 3.4, there are four rules includes,
three of which are meant for plugins:

```
Include crs/crs-setup.conf

Include modsecurity.d/owasp-modsecurity-crs/plugins/*-config.conf
Include modsecurity.d/owasp-modsecurity-crs/plugins/*-before.conf

Include modsecurity.d/owasp-modsecurity-crs/rules/*.conf

Include modsecurity.d/owasp-modsecurity-crs/plugins/*-after.conf
```

The plugin-Folder in the standard CRS distribution is empty. Yet you can
copy your plugin files into that folder. Files with a filename of
the pattern *-before.conf are loaded before all CRS files are being
loaded in the first include line above. Then all the normal CRS
rules are loaded and then finally there is the third Include statement
that loads all the files which follow the filename pattern *-after.conf
from the plugins folder.

This plugin contains three files: 

```
plugins/dummy-config.conf
plugins/dummy-before.conf
plugins/dummy-after.conf
```

## What Rule IDs do the plugins have

Each CRS plugin gets a range of 1,000 rule IDs, from 9,500,000 onwards.
The dummy plugin runs from 9,500,000 to 9,500,999. There is a separate
plugin rule ID registration repo where new plugins can be registered.

https://github.com/coreruleset/plugin-registry

The rule range is meant to be used as follows:

* 9,5XX,000 - 9,5XX,099 : Initialization
* 9,5XX,100 - 9,5XX,499 : Request Rules
* 9,5XX,500 - 9,5XX,999 : Response Rules

## How does this IP-Management Plugin work?
Example commands:
```
curl -A 'asd9qh3nrkl1t9sdvtgbjamla3120ryg-zxnclwq8htkje8gdlqw983y' http://localhost/ip/management/track/add/172.17.0.1
curl -A 'asd9qh3nrkl1t9sdvtgbjamla3120ryg-zxnclwq8htkje8gdlqw983y' http://localhost/ip/management/track/remove/172.17.0.1

```

## License

Copyright (c) 2021-2022 OWASP ModSecurity Core Rule Set project. All rights reserved.

The OWASP ModSecurity Core Rule Set and its official plugins are
distributed under Apache Software License (ASL) version 2.
Please see the enclosed LICENSE file for full details.
