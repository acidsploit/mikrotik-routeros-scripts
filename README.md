mikrotik-routeros-scripts
=========================
My scripts for MikroTik RouterOS

- [OVHDynDNS](#ovhdyndns)
  * [CLI](#cli)


## OVHDynDNS
**OVH DynDNS Update Script for MikroTik RouterOS**

This scrip updates your dynamic IP on your OVH dyndns record, when that IP address changes. This script works specifically for the DynDNS service hosted by OVH.

First you need to create a user to manage access to your DynDNS subdomain. You can do this through your OVH control panel/manager. You will need those credentials for the scipt to work:

    :global ovhddnsuser "<OVH DynDNS USER>"
    :global ovhddnspass "<OVH DynDNS PASS>"
  
Further configuration settings require the interface name on your mikrotik router which holds your public IP. You will also need to specify your OVH DynDNS hostname (something like dynhost.mydomain.com).

    :global theinterface "<INTERFACE THAT HAS YOUR PUBLIC IP>"
    :global ovhddnshost "<OVHDynDNS HOSTNAME>"

Install the [script](OVHDynDNS) as 'ovhddns'.

Define a sheduler to run every 30 minutes. You can lower the interval time, since the script only connects to OVH when your IP has changed.

    /system scheduler 
    add interval=10m name=OVHDynDNS on-event="/system script run ovhddns" policy=read,write,test start-time=startup
    
Thats it! 

Verify you routeros log and do a nslookup to check if everything is working as expected.

___
