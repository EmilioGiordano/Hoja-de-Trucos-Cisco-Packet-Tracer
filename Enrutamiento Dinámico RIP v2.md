#### Ejemplo de configuración RIP
<div align="">
  <img src="https://github.com/user-attachments/assets/6f0e3425-601b-4078-a28c-a7a9f174fdd4" width="50%" alt="Calcular Wildcard" />
</div>



## 1. Configurar el __router central__. Será el que más subredes tendrá que declarar.

### Configurar IP en las interfaces
#### Serial 0/0/0
```bash
Router#configure terminal
Router(config)#interface serial 0/0/0
Router(config-if)#ip address 192.168.10.1 255.255.255.0
Router(config-if)#no shutdown 
```
#### Serial 0/1/0
```bash
Router(config)#interface serial 0/1/0
Router(config-if)#ip address 192.168.20.1 255.255.255.0
Router(config-if)#no shutdown 
```

#### Serial 0/1/1
```bash
Router(config)#interface serial 0/1/1
Router(config-if)#ip address 192.168.30.1 255.255.255.0
Router(config-if)#no shutdown 
```

### Configurar RIP v2
```bash
Router(config)#router rip
Router(config-router)#network 192.168.10.0 
Router(config-router)#network 192.168.20.0
Router(config-router)#network 192.168.30.0
Router(config-router)#version 2
Router(config-router)#no auto-summary 
```


## 2. Configurar el __Router Izquierdo__.
### Configurar IP en las interfaces
#### Serial 0/0/0
```bash
Router#configure terminal
Router(config)#interface serial 0/0/0
Router(config-if)#ip address 192.168.10.2 255.255.255.0
Router(config-if)#no shutdown
```
### Configurar RIP v2
```bash
Router(config)#router rip
Router(config-router)#network 192.168.10.0 
Router(config-router)#version 2
Router(config-router)#no auto-summary 
```

## 3. Configurar el __Router Derecho__.
### Configurar IP en las interfaces
#### Serial 0/1/0
```bash
Router#configure terminal
Router(config)#interface serial 0/1/0
Router(config-if)#ip address 192.168.20.2 255.255.255.0
Router(config-if)#no shutdown
```
### Configurar RIP v2
```bash
Router(config)#router rip
Router(config-router)#network 192.168.20.0
Router(config-router)#version 2
Router(config-router)#no auto-summary 
```

## 4. Configurar el __Router Superior__.
### Configurar IP en las interfaces
#### Serial 0/1/1
```bash
Router(config)#interface serial 0/1/1
Router(config-if)#ip address 192.168.30.1 255.255.255.0
Router(config-if)#no shutdown 
```
### Configurar RIP v2
```bash
Router(config)#router rip
Router(config-router)#network 192.168.30.0
Router(config-router)#version 2
Router(config-router)#no auto-summary 
```
