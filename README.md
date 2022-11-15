# OWASP ModSecurity Core Rule Set - IP-Management Plugin

This repository contains an IP-Management Plugin that provides:
 * Dynamic IP Tracking lists
 * Dynamic IP deny listing
 * Dynamic IP drop listing
 * Dynamic IP allow listing (disabled by default)
 * Dynamic IP engineoff listing (disabled by default)

This plugin contains three files: 

```
plugins/ip-management-config.conf
plugins/ip-management-before.conf
plugins/ip-management-after.conf
```

## How does this IP-Management Plugin work?
The IP Management plugin 

All requests from IPs in monitoring list will be logged (even if they don't trigger
other rules), whether request is allowed or not is not modified by plugin.
All requests from IPs in deny list will be logged and denied.
All request from IPs in drop list be silently dropped (no logging)
All requests from IPs in allow list will be logged and allowed.
All requests from IPs in RuleEngineOff list will have ModSec engine turned off (and 
therefore be allowed)

Rule ID block base: 9,500,000 (range is 1000, thus ID block base +1000)

This plugin initialises the IP collection to be just the IP (not with appended
   user-agent. This behaviour is essential for this plugin to work as expected.
   Plugin must run before IP collection is initialised elsewhere (eg. CRS 901321)
Changing IP collection from CRS 901321:
     initcol:ip=%{remote_addr}_%{tx.ua_hash}
   to:
     initcol:ip=%{remote_addr}
   means that CRS (and any other IP based rules), may have reduced granularity
     as User-Agent will not be used to try and distinguish between multiple


Example commands:
```
curl -A 'asd9qh3nrkl1t9sdvtgbjamla3120ryg-zxnclwq8htkje8gdlqw983y' http://localhost/ip/management/track/add/172.17.0.1
curl -A 'asd9qh3nrkl1t9sdvtgbjamla3120ryg-zxnclwq8htkje8gdlqw983y' http://localhost/ip/management/track/remove/172.17.0.1

curl -A 'asd9qh3nrkl1t9sdvtgbjamla3120ryg-zxnclwq8htkje8gdlqw983y' http://localhost/ip/management/deny/add/172.17.0.1
curl -A 'asd9qh3nrkl1t9sdvtgbjamla3120ryg-zxnclwq8htkje8gdlqw983y' http://localhost/ip/management/deny/remove/172.17.0.1

curl -A 'asd9qh3nrkl1t9sdvtgbjamla3120ryg-zxnclwq8htkje8gdlqw983y' http://localhost/ip/management/allow/add/172.17.0.1
curl -A 'asd9qh3nrkl1t9sdvtgbjamla3120ryg-zxnclwq8htkje8gdlqw983y' http://localhost/ip/management/allow/remove/172.17.0.1

```


The OWASP Core Rule Set (CRS) comes with a plugin structure that allows
to add official and third-party plugins to work with the existing
baseline CRS installation.

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

## What Rule IDs do the plugins have

Each CRS plugin gets a range of 1,000 rule IDs, from 9,500,000 onwards.
The dummy plugin runs from 9,500,000 to 9,500,999. There is a separate
plugin rule ID registration repo where new plugins can be registered.

https://github.com/coreruleset/plugin-registry

The rule range is meant to be used as follows:

* 9,5XX,000 - 9,5XX,099 : Initialization
* 9,5XX,100 - 9,5XX,499 : Request Rules
* 9,5XX,500 - 9,5XX,999 : Response Rules

## License
Copyright (c) 2021-2022 OWASP ModSecurity Core Rule Set project. All rights reserved.

The OWASP ModSecurity Core Rule Set and its official plugins are
distributed under Apache Software License (ASL) version 2.
Please see the enclosed LICENSE file for full details.
