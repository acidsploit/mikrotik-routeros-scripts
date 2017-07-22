mikrotik-routeros-scripts
=========================
Scripts for MikroTik RouterOS


OVHDynDNS - OVH DynDNS Update Script
------------------------------------
This scrip updates your dynamic ip on your dyndns record. This script works specifically for the DynDNS service hosted by OVH. (Which, btw, is the exact same service as DynDNS.org)

First you need to create a user to manage access to your DynDNS subdomain. You can do this through your OVH control panel/manager. You will need those credentials for the scipt to work:

    :global ovhddnsuser "<OVH DynDNS USER>"
    :global ovhddnspass "<OVH DynDNS PASS>"
  
Further configuration settings require the interface name on your mikrotik router which holds your public IP. You will also need to specify your OVH DynDNS hostname (something like dynhost.mydomain.com).

    :global theinterface "<INTERFACE THAT HAS YOU PUBLIC IP>"
    :global ovhddnshost "<OVHDynDNS HOSTNAME>"

Install the script as 'ovhddns'.

Define a sheduler to run every 30 minutes.

    /system scheduler 
    add interval=30m name=OVHDynDNS on-event="/system script run ovhddns" policy=ftp,reboot,read,write,policy,test,password,sniff,sensitive start-time=startup
