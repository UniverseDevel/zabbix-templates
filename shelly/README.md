# Information

List of supported Shelly devices:
- **Shelly 3EM**
- **Shelly Plug and Plug-S**
- **Shelly Motion**
- more to come (I hope)...

Each device has two templates to choose from, one is using HTTP calls to collect device information from API status page and other is collecting device information from MQTT broker "shellies/*/info" topic. Templates were tested and created on Zabbix version 5.4 but some other versions will most likely work as well.

# How to configure HTTP templates

**Requirments**: Zabbix Server, Zabbix Agent (any), Shelly device  
**Host interface**: Type "Agent" with IP/DNS pointing to Shelly device with any Port (leave default).  
**Macros**: Macros in this temaplte are only needed when device has configured username and password. Macro "{$EC_EM_CONSUMPTION}" can be set to total value on your electric company electrometer in Wh at the moment you reset Shelly 3EM data. This value will allow calcucaltion of estimate value on your electrometer in case Shelly 3EM is measuring your main breaker, for this measurment enable item "Electric company electrometer estimate" after you set this macro.
- "{$SHELLY_USER}": optional, default: "admin"
- "{$SHELLY_PASS}": optional, default: ""  
- "{$EC_EM_CONSUMPTION}": optional, default: "0"

# How to configure MQTT templates

**Requirments**: Zabbix Server, Zabbix Agent 2, Shelly device with MQTT configuration, MQTT broker, way how to periodically publish topic to MQTT broker (crontab/Node-RED/else)  
**MQTT broker**: Template requires regular publishing of payload "announce" to topic "shellies/command" for Shelly devices to collect their data and publish them to broker in "shellies/*/info" topic. As an example, tools like crontab with combination of mosquitto-clients binaries or Node-RED flow can be used to achieve this. Ideal publish interval is 30 seconds as template is collecting data from broker every 30 seconds, but this can be adjusted as you need.  
**Agent configuration**: Agent requires parameter "Hostname" to contain name of your Zabbix host that you create with this tempalte linked (multiple Hostnames can be separated with comma ","). This is due to MQTT plugin in Zabbix Agent being Active monitoring.  
**Host name**: Same as value provided in Zabbix Agent 2 configuration in parameter "Hostname".  
**Host interface**: Type "Agent" with IP/DNS and Port pointing to Zabbix Agent 2.  
**Macros**: Macro "{$MQTT_DEVICE_ID}" is mandatory and can be obtained from Shelly device while enabling MQTT (you can check "Use custom MQTT prefix" to display current value, or you can set your own, just make it 25 characters or less to avoid problems). Other macros are only needed when your MQTT broker is on different location or when you configured useranme and password for your broker. Macro "{$EC_EM_CONSUMPTION}" can be set to total value on your electric company electrometer in Wh at the moment you reset Shelly 3EM data. This value will allow calcucaltion of estimate value on your electrometer in case Shelly 3EM is measuring your main breaker, for this measurment enable item "Electric company electrometer estimate" after you set this macro.
- "{$MQTT_DEVICE_ID}": **mandatory**, default: ""
- "{$MQTT_BROKER_HOST}": optional, default: "tcp://localhost:1883"
- "{$MQTT_BROKER_USER}": optional, default: ""
- "{$MQTT_BROKER_PASS}": optional, default: ""  
- "{$EC_EM_CONSUMPTION}": optional, default: "0"

# Important notes

- Using monitoring on Shelly Motion device may with current firmware (v1.1.2) slowly lead to poor performance or no responsivity from device at all. This is due to problems with firmware and this problem is acknowledged by Shelly Support. There is a BETA firmware available that deals with these issues and greatly improves device performance (but not solving it in general yet). This BETA firmware can be applied via following link: "http://\<your device IP\>/ota?url=http://repo.shelly.cloud/firmware/rc/SHMOS-01_build.gbl". Apply this firmware only at your own risk. It is advised to contact Shelly Support for more information and more up-to-date steps.
- When using MQTT temaplte with Shelly Motion, some monitoring items may be unallocated due to current firmware publishing older version of "shellies/*/info" topic that does not contain same values as HTTP API status page.
- Both HTTP and MQTT templates for Shelly 3EM device contain disabled Electrometer N values as these values are optional on device and therefore might need manual enabling.
  Not all templates triggers are actually useful so disable/enable them according your personal need.

# Other sources

https://shelly-api-docs.shelly.cloud/  
https://ticket.shelly.support/  
https://www.zabbix.com/integrations/mqtt  
https://www.zabbix.com/documentation/current/manual/concepts/agent2  
https://www.facebook.com/groups/ShellyIoTCommunitySupport
