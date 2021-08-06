# Information

List of supported Shelly devices:
- **ZyXel Access Point NWA**
- **ZyXel Switch GS1900**
- **ZyXel ZyWALL USG 200**

# How to configure SNMP templates

**Requirments**: Zabbix Server, ZyXel device  
**Host interface**: Type "SNMP" with IP/DNS and Port pointing to ZyXel device.  
**Macros**: Setting macros in this template is option and only needed if SNMP comunity is not public on ZyXel device.  
- "{$SNMP_COMMUNITY}": optional, default: "public"
