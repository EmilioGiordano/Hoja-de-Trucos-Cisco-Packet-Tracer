# 📝 Hoja de Trucos de Redes
## Índice
- [1. Crear VLAN en el Switch](#1-crear-vlan-en-el-switch)
- [2. Asignar puertos del Switch a la VLAN](#2-asignar-puertos-del-switch-a-la-vlan)
- [3. Crear enlace troncal](#3-crear-enlace-troncal)
- [4. Configurar DHCP POOL](#4-configurar-dhcp-pool)
- [5. Configurar PortChannel](#5-configurar-portchannel)
- [6. Enrutamiento Dinámico](#6-enrutamiento-dinámico)
- [7. Subinterfaces de cada VLAN en un router](#7-subinterfaces-de-cada-vlan-en-un-router)
- [Comandos para verificar información](#comandos-para-verificar-información)
---

## 1. Crear VLAN en el Switch
#### ¿Qué es una VLAN(virtual Local Area Network)?
##### Es un método para crear redes lógicas independientes dentro de una misma red física Varias VLAN pueden coexistir en una única red física. Son útiles para reducir el tamaño del dominio de difusión y ayudan en la administración de la red, separando segmentos lógicos de una red de área local que no deberían intercambiar datos usando la red local.
#### Beneficios
* __Escalabilidad__: Es posible agregar dispositivos, ya sean segmentos o dispositivos finales, a cada capa de manera individual, facilitando la escalabilidad.
* __Redundancia__: al tener conectado, por ejemplo, un dispositivo de acceso a dos switches distintos, se minimiza el riesgo de perder la conexión, ya que si una interfaz se corta, se podrá acceder a la red mediante la otra.
* __Rendimiento__: se pueden agregar más interfaces (cables) entre dos dispositivos, para aumentar el ancho de banda. A esto también se le suma la segmentación adecuada que proveen los switches con sus VLANS, optimizando los dominios de broadcast y colisión
* __Seguridad__: la capa de acceso se encarga de controlar qué dispositivos están intentando conectarse antes de darles acceso a la red, más los protocolos y políticas de seguridad de cada capa, disminuyendo el riesgo de que usuarios maliciosos o información contaminada acceda a la red.
* __Administración__: La división en VLANs a partir de la capa de distribución permite además administrar más fácilmente distintos grupos de dispositivos y/o usuarios. Por otro lado, la división en capas, con funcionalidades y configuraciones individuales, otorga una mayor facilidad al momento de configurarlas y administrarlas.
* __Flexibilidad y adaptabilidad__: Dada la segmentación de la red en tres capas, se pueden aplicar nuevas tecnologías en cualquiera de ellas sin afectar negativamente a las otras.
![VLAN diagrama](https://github.com/user-attachments/assets/117b5fd7-45b1-48fb-a943-7146e320b318)
#### Configurar VLANs
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
#### Ejemplo
![image](https://github.com/user-attachments/assets/f95d2c81-53f6-4dc6-a703-60aa356de1d7)


## 3. Crear enlace troncal 
####  Permite comunicar equipos de la misma VLAN pero diferentes Switches

##### Definimos las interfaces(puertos del Switch) en modo troncal. Se recomienda utilizar gigabitEthernet pero fastEthernet también funciona.
```bash
Switch(config)#interface range gigabitEthernet 0/1-2
Switch(config-if-range)#switchport mode trunk
Switch(config-if-range)#switchport trunk native vlan 99
Switch(config-if-range)#no shutdown 
```
#### Ejemplo sobre las Interfaces fastEthernet 0/1 0/2 & 0/3
![image](https://github.com/user-attachments/assets/a30ad9aa-d652-49fa-9b29-ba8587517a13)

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

## 6. Enrutamiento Dinámico
### RIP v2
#### Las IP de las Subredes que estén directamentes conectadas al Router
```bash
Router(config)#router rip
Router(config-router)#network {IP de Subred} Ej: 172.16.0.0
Router(config-router)#network {IP de Subred} Ej: 172.16.0.4
Router(config-router)#network {IP de Subred} Ej: 172.168.0.0
Router(config-router)#version 2
Router(config-router)#no auto-summary 
```
### OSPF
#### Las IP de las Subredes que estén directamentes conectadas al Router
#### Calcular Wildcard:
![image](https://github.com/user-attachments/assets/b909377f-3061-45b8-83f0-abe6ce7c15d5)
```bash
Router(config)#router ospf {ID} 
Router(config-router)#network {IP de Subred} {Wildcard} Ej: 172.16.0.0  0.0.0.255
Router(config-router)#network {IP de Subred} {Wildcard} Ej: 172.16.0.4  0.0.0.255
Router(config-router)#network {IP de Subred} Ej: 172.168.0.0
Router(config-router)#version 2
Router(config-router)#no auto-summary 
```

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
## 7. Subinterfaces de cada VLAN en un router
```bash
configure terminal
interface <interfaz>.<id de vlan> (por ejemplo: gig0/0/0.2)
encapsulation dot1Q <id de vlan>
ip address <ip de gateway> <mascara>
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
