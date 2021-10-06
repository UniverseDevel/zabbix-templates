# Information

List of supported Shelly devices:  
- **Shelly 3EM**  
- **Shelly Plug and Plug-S**  
- **Shelly Motion**  
- **Shelly Bulb RGBW**  
- **Shelly Button1**  
- **Shelly Flood**  
- **Shelly H&T**  

Each template contains three data collection items, "Data collector - HTTP", "Data collector - MQTT" and "Status data". "Status data" item then depends on one of data collectors and it provides values for all other template items so when you decide to use different method of collectiong data, this is a single place to change together with macros. Templates were tested and created on Zabbix version 5.4 but some other versions will most likely work as well.  

# How to configure HTTP templates

**Requirments**: Zabbix Server, Shelly device  
**Host interface**: Type "Agent" with IP/DNS pointing to Shelly device with any Port (leave default).  
**Data collection item**: Edit template item "Status data" and change "Master item" field to "Data collector - HTTP" (this is default so if you did not change this before, you don't have to actually do this).  
**Macros**: Macros in this temaplte are only needed when device has configured username and password. Macro "{$EC_EM_CONSUMPTION}" can be set to total value on your electric company electrometer in Wh at the moment you reset Shelly 3EM data. This value will allow calcucaltion of estimate value on your electrometer in case Shelly 3EM is measuring your main breaker, for this measurment enable item "Electric company electrometer estimate" after you set this macro.  
- "{$SHELLY_USER}": optional, default: "admin"  
- "{$SHELLY_PASS}": optional, default: ""  
- "{$EC_EM_CONSUMPTION}": optional, default: "0", Shelly 3EM only  

# How to configure MQTT templates

**Requirments**: Zabbix Server, Zabbix Agent 2, Shelly device with MQTT configuration, MQTT broker, way how to periodically publish topic to MQTT broker (crontab/Node-RED/else)  
**MQTT broker**: Template requires regular publishing of payload "announce" to topic "shellies/command" for Shelly devices to collect their data and publish them to broker in "shellies/*/info" topic. As an example, tools like crontab with combination of mosquitto-clients binaries or Node-RED flow can be used to achieve this. Ideal publish interval is 30 seconds as template is collecting data from broker every 30 seconds, but this can be adjusted as you need. Other way of announcing as suggested by Elbandi can be found on wiki: https://github.com/UniverseDevel/zabbix-templates/wiki/MQTT-announce.  
**Agent configuration**: Agent requires parameter "Hostname" to contain name of your Zabbix host that you create with this tempalte linked (multiple Hostnames can be separated with comma ","). This is due to MQTT plugin in Zabbix Agent being Active monitoring.  
**Host name**: Same as value provided in Zabbix Agent 2 configuration in parameter "Hostname".  
**Host interface**: Type "Agent" with IP/DNS and Port pointing to Zabbix Agent 2.  
**Data collection item**: Edit template item "Status data" and change "Master item" field to "Data collector - MQTT".  
**Macros**: Macro "{$MQTT_DEVICE_ID}" is mandatory and can be obtained from Shelly device while enabling MQTT (you can check "Use custom MQTT prefix" to display current value, or you can set your own, just make it 25 characters or less to avoid problems). Other macros are only needed when your MQTT broker is on different location or when you configured useranme and password for your broker. Macro "{$EC_EM_CONSUMPTION}" can be set to total value on your electric company electrometer in Wh at the moment you reset Shelly 3EM data. This value will allow calcucaltion of estimate value on your electrometer in case Shelly 3EM is measuring your main breaker, for this measurment enable item "Electric company electrometer estimate" after you set this macro.  
- "{$MQTT_DEVICE_ID}": **mandatory**, default: ""  
- "{$MQTT_BROKER_HOST}": optional, default: "tcp://localhost:1883"  
- "{$MQTT_BROKER_USER}": optional, default: ""  
- "{$MQTT_BROKER_PASS}": optional, default: ""  
- "{$EC_EM_CONSUMPTION}": optional, default: "0", Shelly 3EM only  

# Important notes

- Battery devices like Motion, H&T, Flood and Button1 are proven to be unreliable and collecting their status information even when using MQTT is not easy. These devices tend to go offline to save battery and to avoid overheating (generated heat would cause temperature sensor to report devices own reperature instead of the locations). This also means that most of the time they are not available by HTTP and even MQTT does not provide status updates as requested, but only if temperature or other measured value changes by configured margin. This makes them hard to monitor via Zabbix and unless turned off, most of the time Zabbix templates will trigger No data received.  
- Battery devices templates contain ICMP monitoring that might be disabled as it would most likely always report device unreachable, because it usually turns itelf offline for extended periods of time.  
- Shelly H&T even when using USB addon and setting device to externally powered will go offline to reduce heate produced by device which would interfere with measurments.  
- Template for Shelly 3EM device contain disabled Electrometer N values as these values are optional on device and therefore might need manual enabling.  

Not all templates triggers are actually useful so disable/enable them according your personal need.  

# Other sources

https://share.zabbix.com/shelly-monitoring  

https://shelly-api-docs.shelly.cloud/  
https://ticket.shelly.support/  
https://www.zabbix.com/integrations/mqtt  
https://www.zabbix.com/documentation/current/manual/concepts/agent2  
https://www.facebook.com/groups/ShellyIoTCommunitySupport  
