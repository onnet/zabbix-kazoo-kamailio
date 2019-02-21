# zabbix-kazoo-kamailio
Kamailio Template for Zabbix (defaults for Kazoo)

## Attribution

This is an updated repo of items based on the following Gist from github.com user crashdump:

https://gist.github.com/crashdump/7751564

It has been updated for Kamailio 5.0.3 and Zabbix 3.2.  It has also added the websockets stats items, as well as a method to calculate the total PKG (Private) kamailio memory usage.

## Requirements

This repo assumes that the system has Kamailio (5.0.3) installed, sudo, as well as access to the follow CLI utilities: cut, tr, jq, paste, and bc.  Also, this requires Zabbix 3.2.

## Portability

This has only been tested on CentOS 7.  Other *nix/*bsd systems would probably need to review the tr commands.

## Files

### /etc/zabbix/scripts/kamailio-stats.sh

This script utilizes kamctl and kamcmd to generate two stats temp files (based on a cache ttl) and returns individual metrics based on CLI arguments.

### /etc/zabbix/zabbix_agentd.d/userparameters_kamailio.conf

This file (assuming you are loading it with your zabbix agent config) provides the kamailio[] agent item for the host (which is used in the zabbix template)

### /etc/sudoers.d/zabbix

This file provides the zabbix user the ability to execute the script (specifically the kamctl and kamcmd commands)

### template-kazoo-kamailio.xml

This file is the importable template file for the Zabbix server.

## Installation

### Copy files to host with Zabbix agent and Kamailio

Copy the following files to their locations:

/etc/zabbix/scripts/kamailio-stats.sh
/etc/zabbix/zabbix_agentd.d/userparameters_kamailio.conf (adjust for the locally configured location in zabbix agent)
/etc/sudoers.d/zabbix

Make the kamailio-stats.sh script executable:

chmod +x /etc/zabbix/scripts/kamailio-stats.sh

Apply appropriate permissions to the sudoers.d file:

chmod 400 /etc/sudoers.d/zabbix

Check and remove "Defaults requiretty" from sudo.

Restart zabbix agent (to read in the new userparameters)
```
 yum install bc jq -y
 git clone https://github.com/onnet/zabbix-kazoo-kamailio.git
 cd zabbix-kazoo-kamailio/etc/
 cp sudoers.d/zabbix /etc/sudoers.d/
 cp -r zabbix /etc/
 chmod +x /etc/zabbix/scripts/kamailio-stats.sh
 chmod 400 /etc/sudoers.d/zabbix
 cat /var/log/zabbix/zabbix_agentd.log
 systemctl restart zabbix-agent
 cat /var/log/zabbix/zabbix_agentd.log
```

### Import the template into the Zabbix server

### Assign the template to a host


## Notes

Generally, items are updated every 120 seconds if they are a counter, and 3600 seconds if they are a config parameter.  Also, items that are counters should all be stored as Delta (simple change).  This is a somewhat opinionated setup.
