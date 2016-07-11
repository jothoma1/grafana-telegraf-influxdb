# Freeradius 3 Accounting Content Pack for Graylog (https://www.graylog.org/)

Tested with nxLog/freeradius3/Graylog 2

This content pack provides useful dashboard for auditing freeradius accounting :
* Access from last week
* Top hourly accounting users
* Top hourly callingstation
* Start/Stop/Interim-Update
* VLAN
* NAS

## Includes

* Freeradius 3 linelog accounting configuration (in order to format log in JSON)
* NXLog configuration (change with your graylog server or LB)
* Stream (dont forget to change MyRadiusServer with your host)
* Dashboard

## Requirements

* Freeradius 3 with linelog accounting
* NXLog collecting freeradius logs, other log collectors will work but may require modifying the searches to match the different fields outputted by other collectors

## /etc/raddb/mods-enabled/linelog Example
```
linelog log_accounting {
        filename = ${logdir}/linelog-accounting
        permissions = 0655
        reference = "Accounting-Request.%{%{Acct-Status-Type}:-unknown}"
        Accounting-Request {
Start = "{\"RadacctStatusType\":\"Start\",\"RadacctNASIP\":\"%{NAS-IP-Address}\",\"RadacctUser\":\"%{User-Name}\",\"RadacctSessionId\":\"%{Acct-Session-Id}\",\"RadacctFramedIP\":\"%{Framed-IP-Address}\",\"RadacctNASId\":\"%{NAS-Identifier}\",\"RadacctCiscoAVPair\":\"%{Cisco-AVPair}\",\"RadacctVLAN\":\"%{Tunnel-Private-Group-Id:0}\",\"RadacctTunnelType\":\"%{Tunnel-Type:0}\",\"RadacctMediumType\":\"%{Tunnel-Medium-Type:0}\",\"RadacctCallingStationId\":\"%{Calling-Station-Id}\",\"RadacctCalledStationId\":\"%{Called-Station-Id}\",\"RadacctEventTimestamp\":\"%{Event-Timestamp}\",\"RadacctUniqueSessionId\":\"%{Acct-Unique-Session-Id}\",\"RadacctStrippedUserName\":\"%{Stripped-User-Name}\",\"RadacctRealm\":\"%{Realm}\"}"

Stop = "{\"RadacctStatusType\":\"Stop\",\"RadacctNASIP\":\"%{NAS-IP-Address}\",\"RadacctUser\":\"%{User-Name}\",\"RadacctSessionId\":\"%{Acct-Session-Id}\",\"RadacctFramedIP\":\"%{Framed-IP-Address}\",\"RadacctNASId\":\"%{NAS-Identifier}\",\"RadacctCiscoAVPair\":\"%{Cisco-AVPair}\",\"RadacctVLAN\":\"%{Tunnel-Private-Group-Id:0}\",\"RadacctTunnelType\":\"%{Tunnel-Type:0}\",\"RadacctMediumType\":\"%{Tunnel-Medium-Type:0}\",\"RadacctInputOctets\":\"%{Acct-Input-Octets}\",\"RadacctOutputOctets\":\"%{Acct-Output-Octets}\",\"RadacctInputPackets\":\"%{Acct-Input-Packets}\",\"RadacctOutputPackets\":\"%{Acct-Output-Packets}\",\"RadacctTerminateCause\":\"%{Acct-Terminate-Cause}\",\"RadacctSessionTime\":\"%{Acct-Session-Time}\",\"RadacctDelayTime\":\"%{Acct-Delay-Time}\",\"RadacctCallingStationId\":\"%{Calling-Station-Id}\",\"RadacctCalledStationId\":\"%{Called-Station-Id}\",\"RadacctEventTimestamp\":\"%{Event-Timestamp}\",\"RadacctUniqueSessionId\":\"%{Acct-Unique-Session-Id}\",\"RadacctStrippedUserName\":\"%{Stripped-User-Name}\",\"RadacctRealm\":\"%{Realm}\"}"

Interim-Update = "{\"RadacctStatusType\":\"Interim-Update\",\"RadacctNASIP\":\"%{NAS-IP-Address}\",\"RadacctUser\":\"%{User-Name}\",\"RadacctSessionId\":\"%{Acct-Session-Id}\",\"RadacctFramedIP\":\"%{Framed-IP-Address}\",\"RadacctNASId\":\"%{NAS-Identifier}\",\"RadacctCiscoAVPair\":\"%{Cisco-AVPair}\",\"RadacctVLAN\":\"%{Tunnel-Private-Group-Id:0}\",\"RadacctTunnelType\":\"%{Tunnel-Type:0}\",\"RadacctMediumType\":\"%{Tunnel-Medium-Type:0}\",\"RadacctInputOctets\":\"%{Acct-Input-Octets}\",\"RadacctOutputOctets\":\"%{Acct-Output-Octets}\",\"RadacctInputPackets\":\"%{Acct-Input-Packets}\",\"RadacctOutputPackets\":\"%{Acct-Output-Packets}\",\"RadacctSessionTime\":\"%{Acct-Session-Time}\",\"RadacctDelayTime\":\"%{Acct-Delay-Time}\",\"RadacctCallingStationId\":\"%{Calling-Station-Id}\",\"RadacctCalledStationId\":\"%{Called-Station-Id}\",\"RadacctEventTimestamp\":\"%{Event-Timestamp}\",\"RadacctUniqueSessionId\":\"%{Acct-Unique-Session-Id}\",\"RadacctStrippedUserName\":\"%{Stripped-User-Name}\",\"RadacctRealm\":\"%{Realm}\"}"

                #  Don't log anything for these packets.
                Alive = ""

                Accounting-On = "NAS %{Packet-Src-IP-Address} (%{NAS-IP-Address}) just came online"
                Accounting-Off = "NAS %{Packet-Src-IP-Address} (%{NAS-IP-Address}) just went offline"

                # don't log anything for other Acct-Status-Types.
                unknown = "NAS %{Packet-Src-IP-Address} (%{NAS-IP-Address}) sent unknown Acct-Status-Type %{Acct-Status-Type}"
        }
}
```


## NXLog Example
```
########################################
# Global directives                    #
########################################
User nxlog
Group nxlog

LogFile /var/log/nxlog/nxlog.log
LogLevel WARNING

########################################
# Modules                              #
########################################
<Extension gelf>
    Module      xm_gelf
</Extension>

<Extension json>
    Module      xm_json
</Extension>

<Input in1>
    Module      im_file
    File        '/var/log/radius/linelog-accounting'
    SavePos TRUE
    ReadFromLast TRUE
    Exec        parse_json();
</Input>

<Output out1>
    Module      om_tcp
    Host        graylog.server.com
    Port        12201
    OutputType  GELF_TCP
</Output>

########################################
# Routes                               #
########################################
<Route 1>
    Path        in1 => out1
</Route>

```

## Screenshots

TODO
