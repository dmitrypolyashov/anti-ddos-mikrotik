<h1>Защита от DDOS-атак / SYN Flood protect MIKROTIK</h1>

/ip firewall filter
add chain=forward connection-state=new action=jump jump-target=block-ddos
add chain=forward connection-state=new src-address-list=ddoser dst-address-list=ddosed action=drop
add chain=block-ddos dst-limit=50,50,src-and-dst-addresses/10s action=return
add chain=block-ddos action=add-dst-to-address-list address-list=ddosed address-list-timeout=10m
add chain=block-ddos action=add-src-to-address-list address-list=ddoser address-list-timeout=10m
/
/ip firewall filter
add action=jump chain=forward comment="SYN Flood protect" connection-state=new \
in-interface="ether1" jump-target=SYN-Protect protocol=tcp \
tcp-flags=syn
add action=jump chain=input comment="SYN Flood protect" connection-state=new \
jump-target=SYN-Protect protocol=tcp tcp-flags=syn
add action=return chain=SYN-Protect limit=200,5:packet
add action=drop chain=SYN-Protect
</pre>

<h1>Защита от взлома Микротика (Fail2Ban Mikrotik) / На страже доступа к Микротикам (Fail2Ban Mikrotik</h1>

winbox:
/ip firewall mangle
add action=jump chain=output content="invalid user name or password" jump-target=FB-WB protocol=tcp src-port=8291
/ip firewall mangle
add action=add-dst-to-address-list address-list=FB-WB-BAN address-list-timeout=1d chain=FB-WB dst-address-list=FB-WB-3
/ip firewall mangle
add action=add-dst-to-address-list address-list=FB-WB-3 address-list-timeout=1m chain=FB-WB dst-address-list=FB-WB-2
add action=add-dst-to-address-list address-list=FB-WB-2 address-list-timeout=1m chain=FB-WB dst-address-list=FB-WB-1
add action=add-dst-to-address-list address-list=FB-WB-1 address-list-timeout=1m chain=FB-WB
/ip firewall raw
add action=drop chain=prerouting src-address-list=FB-WB-BAN

Защита WebFig от BruteForce
/ip firewall mangle
add action=jump chain=output content="403 Forbidden" jump-target=FB-WEB protocol=tcp src-port=80
/ip firewall mangle
add action=add-dst-to-address-list address-list=FB-WEB-BAN address-list-timeout=1d chain=FB-WEB dst-address-list=FB-WEB-3
add action=add-dst-to-address-list address-list=FB-WEB-3 address-list-timeout=1m chain=FB-WEB dst-address-list=FB-WEB-2
add action=add-dst-to-address-list address-list=FB-WEB-2 address-list-timeout=1m chain=FB-WEB dst-address-list=FB-WEB-1
add action=add-dst-to-address-list address-list=FB-WEB-1 address-list-timeout=1m chain=FB-WEB
/ip firewall raw
add action=drop chain=prerouting src-address-list=FB-WEB-BAN

Защита WebFig SSL от BruteForce
/ip firewall mangle
add action=jump chain=output jump-target=FB-SSL packet-size=317 protocol=tcp src-port=443
/ip firewall mangle
add action=add-dst-to-address-list address-list=FB-SSL-BAN address-list-timeout=1d chain=FB-SSL dst-address-list=FB-SSL-3
add action=add-dst-to-address-list address-list=FB-SSL-3 address-list-timeout=1m chain=FB-SSL dst-address-list=FB-SSL-2
add action=add-dst-to-address-list address-list=FB-SSL-2 address-list-timeout=1m chain=FB-SSL dst-address-list=FB-SSL-1
add action=add-dst-to-address-list address-list=FB-SSL-1 address-list-timeout=1m chain=FB-SSL
/ip firewall raw
add action=drop chain=prerouting src-address-list=FB-SSL-BAN
