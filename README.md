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

Define a sheduler to run every 30 minutes.

    /system scheduler 
    add interval=30m name=OVHDynDNS on-event="/system script run ovhddns" policy=ftp,reboot,read,write,policy,test,password,sniff,sensitive start-time=startup
    
Thats it! 

Verify you routeros log and do a nslookup to check if everything is working as expected.

___

### CLI 

    /system script
    add name=ovhddns owner=admin policy=read,write,test source=":global ovhddnsuser \"<OVH DynDNS USER>\"\
        \n:global ovhddnspass \"<OVH DynDNS PASS>\"\
        \n:global theinterface \"<INTERFACE THAT HAS YOU PUBLIC IP>\"\
        \n:global ovhddnshost \"<OVHDynDNS HOSTNAME>\"\
        \n:global ipddns [:resolve \$ovhddnshost];\
        \n:global ipfresh [ /ip address get [/ip address find interface=\$theinterface ] address ]\
        \n:if ([ :typeof \$ipfresh ] = nil ) do={\
        \n   :log info (\"OVHDynDNS: No ip address on \$theinterface .\")\
        \n} else={\
        \n   :for i from=( [:len \$ipfresh] - 1) to=0 do={ \
        \n      :if ( [:pick \$ipfresh \$i] = \"/\") do={ \
        \n    :set ipfresh [:pick \$ipfresh 0 \$i];\
        \n      } \
        \n}\
        \n \
        \n:if (\$ipddns != \$ipfresh) do={\
        \n   :log info (\"OVHDynDNS: DNS RECORD IP = \$ipddns\")\
        \n   :log info (\"OVHDynDNS: CURRENT IP = \$ipfresh\")\
        \n   :log info (\"OVHDynDNS: UPDATING OVHDDNS RECORD!\")\
        \n   :global str \"/nic/update\\\?system=dyndns&hostname=\$ovhddnshost&myip=\$ipfresh&wildcard=OFF&backmx=NO&mx=NOCHG\"\
        \n   /tool fetch address=ovh.com src-path=\$str mode=https user=\$ovhddnsuser password=\$ovhddnspass dst-path=(\"/OVHDynDNS.\".\$ovhddnshost)\
        \n   :delay 1\
        \n   :global str [/file find name=\"OVHDynDNS.\$ovhddnshost\"];\
        \n   /file remove \$str\
        \n   :global ipddns \$ipfresh\
        \n   :log info \"OVHDynDNS: IP updated to \$ipfresh!\"\
        \n    } else={\
        \n     :log info \"DynDNS: dont need changes\";\
        \n    }\
        \n}"
        
    /system scheduler
    add interval=30m name=OVHDynDNS on-event="/system script run ovhddns" policy=read,write,test start-time=startup
    
___
