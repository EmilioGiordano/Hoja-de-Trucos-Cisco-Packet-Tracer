## Ejemplo de configuración del protocolo de enrutamiento dinámico OSPF para 3 Routers.
<div align="">
  <img src="https://github.com/user-attachments/assets/a64cc255-1060-4849-a0df-37550dcdb2c3" width="50%" alt="Calcular Wildcard" />
</div>

## Índice
- [1. Configurar las interfaces de los Routers](#1-configurar-las-interfaces-de-los-routers)
  - [Interfaces del Router 1](#router-1)
  - [Interfaces del Router 2](#router-2)
  - [Interfaces del Router 3](#router-3)
  

  
- [2. Configurar el protocolo OSPF en los 3 Routers.](#2-configurar-el-protocolo-ospf-en-los-3-routers)


## Cada Router debe declarar 3 subredes como vecinas en el protocolo OSPF

## 1. Configurar las interfaces de los Routers.

### Router 1
#### Serial 0/0/0
```bash
Router#configure terminal
Router(config)#interface serial 0/0/0
Router(config-if)#ip address 192.168.10.1 255.255.255.252
Router(config-if)#no shutdown
Router(config-if)#exit
```
#### Serial 0/0/1
```bash
Router(config)#interface serial 0/0/1
Router(config-if)#ip address 192.168.10.5 255.255.255.252
Router(config-if)#no shutdown
Router(config-if)#exit
```

#### fastEthernet 0/0
```bash
Router(config-if)#ip address 192.16.1.17 255.255.255.240
Router(config-if)#no shutdown
Router(config-if)#exit
```

### __Router 2__
#### Serial 0/0/0
```bash
Router#configure terminal
Router(config)#interface serial 0/0/0
Router(config-if)#ip address 192.168.10.2 255.255.255.252
Router(config-if)#no shutdown
Router(config-if)#exit
```
#### Serial 0/0/1
```bash
Router#configure terminal
Router(config)#interface serial 0/0/1
Router(config-if)#ip address 192.168.10.9 255.255.255.252
Router(config-if)#no shutdown
Router(config-if)#exit
```
#### fastEthernet 0/0
```bash
Router#configure terminal
Router(config)#interface fastEthernet 0/0
Router(config-if)#ip address 10.10.10.1 255.255.255.0
Router(config-if)#no shutdown
Router(config-if)#exit
```



### __Router 3__
#### Serial 0/0/0
```bash
Router#configure terminal
Router(config)#interface serial 0/0/0
Router(config-if)#ip address 192.168.10.6 255.255.255.252
Router(config-if)#no shutdown
Router(config-if)#exit
```
#### Serial 0/0/1
```bash
Router(config)#interface serial 0/0/1
Router(config-if)#ip address 192.168.10.10 255.255.255.252 
Router(config-if)#no shutdown
Router(config-if)#exit
```
#### fastEthernet 0/0
```bash
Router(config)#interface fastEthernet 0/0
Router(config-if)#ip address 172.16.1.33 255.255.255.248
Router(config-if)#no shutdown
Router(config-if)#exit
```


## 2. Configurar el protocolo OSPF en los 3 Routers.
#### Ya definidas las interfaces, debemos proceder a configurar el protocolo de enrutamiento dinánmico OSPF en cada Router

