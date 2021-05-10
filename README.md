# Popis nastavení

Jak je vidět na obrázku síť 192.168.123.0/24 byla rozdělena na dva subnety, aby každý subnet co nejlépe odpovídal počtu požadovaných hostů v subnetu.

Subnet 192.168.123.0/27 je určen pro síť kde jsou propojeny počitači ve switchi s routerem R1. Subnet 192.168.123.32/30 slouží pro propojení routeru R1 a R2. 
Routování je zajišťeno pomocí statických cest. Na routeru R2 je vytvořen NAT v režimu PAT (maškaráráda), tedy všechny zprávy, které směrují za R2 se překládají na ip adresu rozhraní G0/1 na R2.

![mapaSite](https://github.com/smildas/PSI-ukol4/blob/master/mapa.png)

## Nastavení jednotlivých prvků

### R1

```shell
conf t
host Router-1
int g0/0
ip add 192.168.123.33 255.255.255.252
no shut
exit
int g1/0
ip add 192.168.123.1 255.255.255.224
no shut
exit
ip route 0.0.0.0 0.0.0.0 192.168.123.34
```

### R2

```shell
conf t
int g0/0
ip add 192.168.123.34 255.255.255.252
ip nat inside
no shut
exit
int g1/0
ip add dhcp
ip nat outside
no shut
exit
ip nat inside source list 1 interface GigabitEthernet1/0 overload
ip route 192.168.123.0 255.255.255.224 192.168.123.33
access-list 1 permit any
```

### PC1
```shell
ip 192.168.123.2/27 192.168.123.1
ip dns 8.8.8.8
```

### PC2
```shell
ip 192.168.123.3/27 192.168.123.1
ip dns 8.8.8.8
```
