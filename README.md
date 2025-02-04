# 📝 Hoja de Trucos de Redes
## Índice
- [1. Crear VLAN en el Switch](#1-crear-vlan-en-el-switch)
- [2. Asignar puertos del Switch a la VLAN](#2-asignar-puertos-del-switch-a-la-vlan)
- [3. Crear enlace troncal](#3-crear-enlace-troncal)
- [4. Configurar DHCP POOL](#4-configurar-dhcp-pool)
- [5. Configurar PortChannel](#5-configurar-portchannel)
- [6. Enrutamiento Dinámico](#6-enrutamiento-dinámico)
  - [RIP v.2](#rip-v2)
  - [OSPF](#ospf)
- [7. Subinterfaces de cada VLAN en un router](#7-subinterfaces-de-cada-vlan-en-un-router)
- [8. Comandos para verificar información](#8-comandos-para-verificar-información)
---

## 1. Crear VLAN en el Switch
### ¿Qué es una VLAN(Virtual Local Area Network)?
Es un método para crear redes lógicas independientes dentro de una misma red física. Varias VLAN pueden coexistir en una única red física. Son útiles para reducir el tamaño del dominio de difusión y ayudan en la administración de la red, separando segmentos lógicos de una red de área local que no deberían intercambiar datos usando la red local.
### Beneficios
* __Escalabilidad__: Es posible agregar dispositivos, ya sean segmentos o dispositivos finales, a cada capa de manera individual, facilitando la escalabilidad.
* __Redundancia__: al tener conectado, por ejemplo, un dispositivo de acceso a dos switches distintos, se minimiza el riesgo de perder la conexión, ya que si una interfaz se corta, se podrá acceder a la red mediante la otra.
* __Rendimiento__: se pueden agregar más interfaces (cables) entre dos dispositivos, para aumentar el ancho de banda. A esto también se le suma la segmentación adecuada que proveen los switches con sus VLANS, optimizando los dominios de broadcast y colisión
* __Seguridad__: la capa de acceso se encarga de controlar qué dispositivos están intentando conectarse antes de darles acceso a la red, más los protocolos y políticas de seguridad de cada capa, disminuyendo el riesgo de que usuarios maliciosos o información contaminada acceda a la red.
* __Administración__: La división en VLANs a partir de la capa de distribución permite además administrar más fácilmente distintos grupos de dispositivos y/o usuarios. Por otro lado, la división en capas, con funcionalidades y configuraciones individuales, otorga una mayor facilidad al momento de configurarlas y administrarlas.
* __Flexibilidad y adaptabilidad__: Dada la segmentación de la red en tres capas, se pueden aplicar nuevas tecnologías en cualquiera de ellas sin afectar negativamente a las otras.
<div align="center">
  <img src="https://github.com/user-attachments/assets/117b5fd7-45b1-48fb-a943-7146e320b318" alt="VLAN diagrama" width="400"/>
</div>

### Configurar VLANs
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
#### Ejemplo de topología
<div align="center">
  <img src="https://github.com/user-attachments/assets/f95d2c81-53f6-4dc6-a703-60aa356de1d7" alt="VLAN diagrama" width="400"/>
</div>



## 3. Crear enlace troncal 
Permite comunicar equipos de la misma VLAN pero diferentes Switches
Definimos las interfaces(puertos del Switch) en modo troncal. Se recomienda utilizar gigabitEthernet pero fastEthernet también funciona.

```bash
Switch(config)#interface range gigabitEthernet 0/1-2
Switch(config-if-range)#switchport mode trunk
Switch(config-if-range)#switchport trunk native vlan 99
Switch(config-if-range)#no shutdown 
```
#### Ejemplo sobre las Interfaces fastEthernet 0/1 0/2 & 0/3

<div align="center">
  <img src="https://github.com/user-attachments/assets/a30ad9aa-d652-49fa-9b29-ba8587517a13" alt="Enlace troncal"/>
</div>


## 4. Configurar DHCP POOL
#### VLAN 10
```bash
Router(config)# ip dhcp pool VLAN20(Nombre de la VLAN)
Router(dhcp-config)# network 172.17.0.0 255.255.192.0
Router(dhcp-config)# default-router 172.17.0.1
Router(dhcp-config)# dns-server [ip dns server]
Router(dhcp-config)# exit
Router(config)# ip dhcp excluded-address 172.17.0.1 
```
##### Excluir intervalo de direcciones IP: excluirá las direcciones del intervalo [172.17.0.1; 172.17.0.10]
```bash
Router(config)# ip dhcp excluded-address 172.17.0.1 172.17.0.10
```
#### VLAN 20 
```bash
Router(config)# ip dhcp pool VLAN20(NAME)
Router(dhcp-config)# network 172.17.0.0 255.255.192.0
Router(dhcp-config)# default-router 172.17.0.1
Router(dhcp-config)# dns-server [ip dns server]
Router(dhcp-config)# exit
Router(config)#ip dhcp excluded-address 172.16.1.128 172.16.1.132
```

## 5. Configurar PortChannel
#### Acceder al Switch y entrar en modo configuración global
```bash
Switch> enable
Switch# configure terminal
```
#### Crear y configurar PortChannel
Configura el `Port-channel1` y define los parámetros del trunk:
```bash
Switch(config)# interface Port-channel1
Switch(config-if)# description TRUNK
Switch(config-if)# switchport trunk native vlan 99
Switch(config-if)# switchport mode trunk
```
#### Configurar interfaces físicas
Configurar las interfaces en el rango de `FastEthernet0/1` a `FastEthernet0/4` para que sean parte del trunk y pertenezcan al Port-channel1:
```bash
Switch(config)# interface range FastEthernet0/1-4
Switch(config-if-range)# description TRUNK
Switch(config-if-range)# switchport trunk native vlan 99
Switch(config-if-range)# switchport mode trunk
Switch(config-if-range)# channel-group 1 mode active
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
<!-- [Ejemplo práctico de Enrutamiento RIP v2](./Enrutamiento%20Dinámico%20RIP%20v2.md) -->

### OSPF
Se deben declarar las subredes que estén __directamente conectadas__ a cada Router 
##### Calcular Wildcard:
<div align="center">
  <img src="https://github.com/user-attachments/assets/b909377f-3061-45b8-83f0-abe6ce7c15d5" alt="Calcular Wildcard" />
</div>
ID del proceso: número entre 1 y 65535. No se necesita coincidencia de ID con otros routers OSPF

```bash
Router(config)#router ospf {ID del proceso}
Router(config-router)#version 2
Router(config-router)#network {IP de Subred 1} {Wildcard} area 0 
Router(config-router)#network 192.168.10.0 0.0.0.3 area 0 
Router(config-router)#network 192.168.10.8 0.0.0.3  area 0
Router(config-router)#network 10.10.10.0 0.0.0.255 area 0
Router(config-router)#no auto-summary 
```
[`Ejemplo práctico de Enrutamiento OSPF`](./Enrutamiento%20Dinámico%20OSPF.md)


#### Comandos de verificación de OSPF
```bash
Router>enable
Router#show ip protocols
```
__Muestra la ID del proceso OSPF, la ID del router, el router de red que se encuentra notificando y la distancia administrativa.__

```bash
Router>enable
Router#Show ip ospf
```
__Muestra la ID del proceso OSPF, la ID del router, información del área OSPF y la última vez que se calculó el algoritmo SPF.__

```bash
Router>enable
Router#show ip ospf interface
```
__Muestra el intervalo de saludo y el intervalo muerto.__

## 7. Subinterfaces de cada VLAN en un router
```bash
Router>enable
Router#configure terminal
Router(config)#interface <interfaz>.<id de vlan>  # Ej: gig0/0/0.2
Router(config-if)#encapsulation dot1Q <id de vlan>
Router(config-if)#ip address <ip de gateway> <mascara>
```
<div align="center">
  <img src="https://github.com/user-attachments/assets/9f13d348-f5f9-4ee6-a4f9-63ba22c6dca5" alt="Calcular Wildcard" />
</div>

## 8. Comandos para verificar información
### Switch
```bash
Switch>enable
Switch#show vlan                      # Muestra la configuración de VLANs
Switch#show interfaces trunk          # Muestra información sobre interfaces trunk
Switch#show interfaces status         # Muestra el estado de las interfaces
Switch#show mac address-table         # Muestra la tabla de direcciones MAC
Switch#show spanning-tree             # Muestra el estado del protocolo Spanning Tree
```

### Router
__Mostrar interfaces y sus estados:__
```bash
Router#show ip interface brief                      # Muestra interfaces y sus estados
Router#show ip route                                # Muestra la configuración de rutas IP
Router#show run interface GigabitEthernet0/0.171    # Muestra configuración de subinterface 171
Router#show run interface GigabitEthernet0/0.172    # Muestra configuración de subinterface 172
Router#show ip protocols                            # Muestra información sobre protocolos de enrutamiento
Router#show ip ospf interface                       # Muestra información sobre interfaces OSPF
```

---
✍️ **Autor:** Emilio Giordano  
🔗 Enlace al [repositorio](https://github.com/EmilioGiordano/Hoja-de-Trucos-Cisco-Packet-Tracer)  
---
