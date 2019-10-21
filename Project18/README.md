Выполнение теоретической части ДЗ18.
Для каждой из указанных в задании сетей указал кол-во хостовых адресов, а также указал Broadcast адрес.
Указал свободные подсети (максимально большие).
Явных ошибок при разбиении не вижу, однако явно нужно еще стыковочные сети для связи маршрутизаторов (обычно используется /30, хотя зависит от физической топологии). Причем в Vagrant-файле такие сети применяются.

Сеть office1
- 192.168.2.0/26 - dev
62 host addresses, BC address 192.168.2.63

- 192.168.2.64/26 - test servers
62 host addresses, BC address 192.168.2.127

- 192.168.2.128/26 - managers
62 host addresses, BC address 192.168.2.191

- 192.168.2.192/26 - office hardware
62 host addresses, BC address 192.168.2.255

Сеть office2
- 192.168.1.0/25 - dev
126 host addresses, BC address 192.168.1.127

- 192.168.1.128/26 - test servers
62 host addresses, BC address 192.168.1.191

- 192.168.1.192/26 - office hardware
62 host addresses, BC address 192.168.1.255


Сеть central
- 192.168.0.0/28 - directors
14 host addresses, BC address 192.168.0.15

# 192.168.0.16/28 - СВОБОДНАЯ ПОДСЕТЬ
14 host addresses, BC address 192.168.0.31

- 192.168.0.32/28 - office hardware
14 host addresses, BC address 192.168.0.47

# 192.168.0.48/28 - СВОБОДНАЯ ПОДСЕТЬ
14 host addresses, BC address 192.168.0.63

- 192.168.0.64/26 - wifi
62 host addresses, BC address 192.168.0.127

# 192.168.0.128/25 - СВОБОДНАЯ ПОДСЕТЬ
126 host addresses, BC address 192.168.0.255

