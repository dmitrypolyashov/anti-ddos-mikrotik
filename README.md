# anti-ddos-mikrotik
<h1>Защита от DDOS-атак и SYN Flood protect MIKROTIK</h1>

Конфигурация в работе на HAP AC:
/ip firewall filter
add chain=forward connection-state=new action=jump jump-target=block-ddos
add chain=forward connection-state=new src-address-list=ddoser dst-address-list=ddosed action=drop
add chain=block-ddos dst-limit=50,50,src-and-dst-addresses/10s action=return
add chain=block-ddos action=add-dst-to-address-list address-list=ddosed address-list-timeout=10m
add chain=block-ddos action=add-src-to-address-list address-list=ddoser address-list-timeout=10m
/
/ip firewall filter
add action=jump chain=forward comment="SYN Flood protect" connection-state=new \
in-interface="ether1-gateway" jump-target=SYN-Protect protocol=tcp \
tcp-flags=syn
add action=jump chain=input comment="SYN Flood protect" connection-state=new \
jump-target=SYN-Protect protocol=tcp tcp-flags=syn
add action=return chain=SYN-Protect limit=200,5:packet
add action=drop chain=SYN-Protect
