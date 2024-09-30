# 📝 Hoja de Trucos de Redes
## Índice
- [1. Crear VLAN en el Switch](#1-crear-vlan-en-el-switch)
- [2. Asignar puertos del Switch a la VLAN](#2-asignar-puertos-del-switch-a-la-vlan)
- [3. Crear enlace troncal](#3-crear-enlace-troncal)
- [4. Configurar DHCP POOL](#4-configurar-dhcp-pool)
- [5. Configurar PortChannel](#5-configurar-portchannel)
- [6. OSPF](#6-ospf)
- [Comandos para verificar información](#comandos-para-verificar-información)
---

## 1. Crear VLAN en el Switch
####  VLAN 10: Administración
```bash
Switch> enable
Switch#configure terminal
Switch(config)#vlan 10
Switch(config-vlan)#name Administracion
Switch(config-vlan)#exit
```
####  VLAN 20: RRHH
```bash
Switch(config)#vlan 20
Switch(config-vlan)#name RRHH
Switch(config-vlan)#exit
```

## 2. Asignar puertos del Switch a la VLAN
####  VLAN 10 para los puertos fastEthernet 0/1 al 0/6(o cualquier puerto que se desee)
``` bash

Switch(config)#interface range fastEthernet 0/1-6
Switch(config-if-range)#switchport mode access 
Switch(config-if-range)#switchport access vlan 10
```
####  VLAN 20 para los puertos fastEthernet 0/7 al 0/10(o cualquier puerto que se desee)
``` bash
Switch(config)#interface range fastEthernet 0/7-10
Switch(config-if-range)#switchport mode access 
Switch(config-if-range)#switchport access vlan 20
```

## 3. Crear enlace troncal 
####  Permite comunicar equipos de la misma VLAN pero diferentes Switches
##### Primero creamos la VLAN en el Switch
```bash
Switch(config)#vlan 99
Switch(config-vlan)# exit
```
##### Luego definimos la interfaz(puerto) en modo troncal. Se recomienda utilizar gigabitEthernet pero fastEthernet también funciona.
```bash
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
####  
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

## 6. OSPF
#### Verificación de OSPF: comandos adicionales
##### Muestra la ID del proceso OSPF, la ID del router, el router de red que se encuentra notificando y la distancia administrativa.
```bash
Show ip protocols
```
##### Muestra la ID del proceso OSPF, la ID del router, información del área OSPF y la última vez que se calculó el algoritmo SPF.
```bash
Show ip ospf
```
##### Muestra el intervalo de saludo y el intervalo muerto.
```bash
Show ip ospf interface
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
