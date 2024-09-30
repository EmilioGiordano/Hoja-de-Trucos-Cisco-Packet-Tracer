# 📝 Hoja de Trucos de Redes
## Índice
- [Crea VLAN en el Switch](#1-crear-vlan-en-el-switch)
- [Asignar puertos del Switch a la VLAN](#2-asignar-puertos-del-switch-a-la-vlan)
- [Crear enlace troncal](#3-crear-enlace-troncal)
- [Configurar DHCP POOL](#4-configurar-dhcp-pool)
- [Configurar PortChannel](#5-configurar-portchannel)
- [Comandos para verificar información](#comandos-para-verificar-información)
---

## 1. Crear VLAN en el Switch

```bash
Switch> enable
Switch#configure terminal
Switch(config)#vlan 10
Switch(config-vlan)#name Administracion
```

## 2. Asignar puertos del Switch a la VLAN
```bash
Switch(config)#interface range fastEthernet 0/1-6
Switch(config-if-range)#switchport mode access 
Switch(config-if-range)#switchport access vlan 172
```

## 3. Crear enlace troncal 
####  Permite comunicar equipos de la misma VLAN pero diferentes Switches
```bash
Switch(config)#vlan 99
Switch(config-vlan)# exit
Switch(config)#interface range gigabitEthernet 0/1-2
Switch(config-if-range)#switchport mode trunk
Switch(config-if-range)#switchport trunk native vlan 99
Switch(config-if-range)#no shutdown 
```

## 4. Configurar DHCP POOL
```bash
Router(config)# ip dhcp pool VLAN171(NAME)
Router(dhcp-config)# network 172.17.0.0 255.255.192.0
Router(dhcp-config)# default-router 172.17.0.1
Router(dhcp-config)# dns-server [ip dns server]
Router(dhcp-config)# exit
//Excluye todos estos valores, empieza desde .11
Router(config)# ip dhcp excluded-address 172.17.0.1 172.17.0.10

Router(config)# ip dhcp pool VLAN171(NAME)
Router(dhcp-config)# network 172.17.0.0 255.255.192.0
Router(dhcp-config)# default-router 172.17.0.1
Router(dhcp-config)# dns-server [ip dns server]
Router(dhcp-config)# exit
//Excluye todos estos valores, empieza desde .11
Router(config)# ip dhcp excluded-address 172.17.0.1 172.17.0.10
```
## 5. Configurar PortChannel 
####  Permite comunicar equipos de la misma VLAN pero diferentes Switches
```bash
interface Port-channel1
description TRUNK
switchport trunk native vlan 99
switchport mode trunk

interface range FastEthernet0/1-4
description TRUNK
switchport trunk native vlan 99
switchport mode trunk
channel-group 1 mode active
```

## Comandos para verificar información
### Switch
```bash
show vlan
show interfaces trunk
show interfaces status
```

### Router
#### Mostrar interfaces y sus estados:
```bash
Router# show ip interface brief
```

#### Mostrar configuración de rutas IP:
```bash
Router# show ip route
```
#### Mostrar configuración de subinterfaces en el router:
```bash
Router# show run interface GigabitEthernet0/0.171
Router# show run interface GigabitEthernet0/0.172
```
