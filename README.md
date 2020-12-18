# EIGRP

# Лабораторная работа: EIGRP.
# Задание:
1. В офисе С.-Петербург настроить EIGRP
2. R32 получает только маршрут по-умолчанию
3. R16-17 анонсируют только суммарные префиксы
4. Использовать EIGRP named-mode для настройки сети

# Решение:

# Схема сегмента сети:

![](https://github.com/dmitriyklimenkov/EIGRP/blob/main/%D0%A1%D1%85%D0%B5%D0%BC%D0%B0%20EIGRP.PNG)

# 1. Конфигурация R16, R17.
Настройку OSPF произведем на примере маршрутизатора R16. На нем настроим процесс EIGRP. По заданию необходимо использовать named mode. Так же по заданию необходимо, чтобы R32 получал только маршрут по умолчанию, поэтому на интерфейсе e0/3 пропишем summary-address.
```
!
router eigrp Saint-P
 !
 address-family ipv4 unicast autonomous-system 4
  !
  af-interface Ethernet0/0
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/1
   summary-address 31.52.6.0 255.255.255.0
   summary-address 35.52.0.0 255.255.252.0
  exit-af-interface
  !
  af-interface Ethernet0/3
   summary-address 0.0.0.0 0.0.0.0
  exit-af-interface
  !
  topology base
  exit-af-topology
  network 31.52.0.0 0.0.3.255
  network 31.52.6.0 0.0.0.3
  network 31.52.6.8 0.0.0.3
  network 31.52.6.12 0.0.0.3
  eigrp router-id 1.1.1.16
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 4
  !
  af-interface Ethernet0/3
   summary-address ::/0
  exit-af-interface
  !
  topology base
  exit-af-topology
  eigrp router-id 0.0.0.16
 exit-address-family
!
```
На R17 настройки аналогичные.

# 2. Конфигурация R18

На R18 будут аналогичные настройки. Только здесь для примера пропишем маршрут по умолчанию в сторону R24.
```
!
router eigrp Saint-P
 !
 address-family ipv4 unicast autonomous-system 4
  !
  topology base
  exit-af-topology
  network 31.52.6.0 0.0.0.3
  network 31.52.6.4 0.0.0.3
  eigrp router-id 1.1.1.18
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 4
  !
  topology base
  exit-af-topology
  eigrp router-id 0.0.0.18
 exit-address-family
!
ip forward-protocol nd
!
!
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 194.14.123.10
!
ipv6 route ::/0 200C:C0FE:1111:50::2
!
```

# 3. Проверка

Посмотрим таблицу мрашрутизации R32.

![](https://github.com/dmitriyklimenkov/EIGRP/blob/main/R32%20table.PNG)

Видим, что приходит только маршрут по умолчанию.

Проверим таблицу мрашрутизации R17.

![](https://github.com/dmitriyklimenkov/EIGRP/blob/main/R17%20table.PNG)
