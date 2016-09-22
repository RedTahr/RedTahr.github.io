countermeasures for SNMP:
*block port 161 TCP/UDP or allow it to be used by specific machines.
*IPSec filtering
*Limit access to null sessions

countermeasures for DNS:
*configure DNS Zone transfer to explicit servers
*ensure nonpublic hostnames are not referenced to IP within the DNS zone files or publicly accessible DNS servers
*check both internal/external DNS servers
*ensure that HINFO and other records do not appears in DNS zone files