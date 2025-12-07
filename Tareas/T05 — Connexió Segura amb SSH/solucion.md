# Guía: Conexión Cliente-Servidor Ubuntu–Windows con SSH, Proxy y Análisis de Tráfico

## Introducción
En esta práctica se configurarán dos máquinas virtuales en VirtualBox:

- **Servidor:** Ubuntu  
- **Cliente:** Windows  

El objetivo es establecer comunicación entre ambas mediante **SSH**, implementar un **proxy Squid** en el servidor y analizar el tráfico con **Wireshark**.  
Todo se realizará mediante comandos para comprender el funcionamiento interno de la red y los servicios.

---

## Estructura de la guía

### 1. Configuración de red en VirtualBox
- Adaptadores: **NAT + Adaptador en modo Anfitrión** en ambas máquinas.
- Verificación de conectividad entre cliente y servidor.

### 2. Instalación y configuración del servicio SSH en Ubuntu
- Instalación de **openssh-server**.
- Verificación del servicio y apertura del puerto correspondiente.

### 3. Conexión desde Windows al servidor Ubuntu mediante SSH
- Uso del comando `ssh` desde **PowerShell** o **CMD**.
- Prueba de autenticación.

### 4. Instalación y configuración del proxy (Squid) en Ubuntu
- Instalación de **Squid**.
- Configuración básica para permitir tráfico del cliente.
- Verificación del funcionamiento del proxy.

### 5. Captura y análisis del tráfico con Wireshark
- Instalación de Wireshark en ambas máquinas (o al menos en una).
- Uso de filtros para visualizar tráfico

---

##Preparacion de las maquinas y sus adaptadores
Crearemos e instalaremos las maquinas en nuestro equipo teniendo en cuenta esto:

<img src="IMG/1.png" alt="..." width="400" height="auto">

Tanto el servidor como el cliente seran configurados con los 2 adaptadores que usaremos para esta practica, los cuales son `NAT` y `Anfitrion`.

Indice:






....

Una vez las maquinas  estan instaladas iniciaremos con el server

## Paso 1: Actualización del sistema en el servidor Ubuntu

### Introducción
Antes de instalar cualquier servicio, es fundamental asegurarse de que el sistema esté actualizado.  
Esto garantiza que todos los paquetes y dependencias estén en su versión más reciente, reduciendo problemas de compatibilidad y seguridad.

---

## Instrucciones detalladas

### 1. Accede al servidor Ubuntu
Inicia sesión en la máquina virtual Ubuntu desde la consola.

### 2. Actualiza la lista de paquetes disponibles y actualizaciones en general
Ejecuta el siguiente comando:

<img src="IMG/5.png" alt="..." width="400" height="auto">

```
sudo apt update && sudo apt upgrade -y
```

Explicación:
Este comando sincroniza la lista de paquetes locales con los repositorios oficiales, mostrando si hay actualizaciones disponibles.

## Notas importantes

- Asegúrate de tener conexión a Internet en la máquina Ubuntu antes de ejecutar estos comandos.
- Si el sistema pide reinicio después de la actualización, usa:
```
sudo reboot
```

---

## Paso 2: Configuración de Netplan para habilitar DHCP en ambos adaptadores

### Introducción:
En este paso configuraremos la red del servidor Ubuntu para que ambos adaptadores (NAT y Adaptador en modo Anfitrión) utilicen DHCP. Esto permitirá que cada interfaz obtenga automáticamente una dirección IP, facilitando la conectividad tanto con Internet como con la red interna entre cliente y servidor.

---

## Instrucciones detalladas

### 1. Identificar las interfaces de red
Ejecuta:
```
ip a
```
Explicación:
Este comando lista todas las interfaces de red. Normalmente verás algo como enp0s3 (NAT) y enp0s8 (Anfitrión).

### 2. Editar el archivo de configuración de Netplan con nano
El archivo suele estar en:
```
sudo nano /etc/netplan/50-cloud-init.yaml
```
Explicación:
Aquí definiremos que ambas interfaces usen DHCP.

### 3. Configurar el YAML para DHCP en ambas interfaces
Sustituye el contenido para dejarlo asi:

<img src="IMG/3.png" alt="..." width="400" height="auto">

Explicación:
enp0s3 → interfaz NAT (acceso a Internet).
enp0s8 → interfaz en modo anfitrión (comunicación interna).
dhcp4: true → habilita DHCP para IPv4.

### 4. Aplicar los cambios
Ejecuta:
```
sudo netplan apply
```
Explicación:
Aplica la nueva configuración de red.

### 5. Verificar las direcciones IP asignadas
Ejecuta:
```
ip a
```

Explicación:
Comprueba que cada interfaz tiene una IP distinta (una del rango NAT y otra del rango anfitrión).

## Notas importantes

- Respeta la indentación en el archivo YAML (espacios, no tabuladores).
- Si el archivo no se llama `50-cloud-init.yaml`, puede ser `01-netcfg.yaml` u otro. Verifica con:
```
ls /etc/netplan/
```

OJO: Si no se asignan IPs, reinicia la máquina:
```
sudo reboot
```

---

## Paso 3: Instalación y configuración del servicio SSH en Ubuntu

### Introducción:
El servicio SSH (Secure Shell) permite la conexión remota segura entre sistemas. En este paso instalaremos el servidor SSH en Ubuntu para que el cliente Windows pueda conectarse mediante comandos.

---

### 1. Instalar el servidor SSH
Ejecuta:

<img src="IMG/6.png" alt="..." width="400" height="auto">

```
sudo apt install openssh-server -y
```
Explicación:
Instala el paquete openssh-server que habilita el servicio SSH en Ubuntu. El parámetro -y acepta automáticamente la instalación.

### 2. Verificar que el servicio está activo
Ejecuta:

<img src="IMG/7.png" alt="..." width="400" height="auto">

```
sudo systemctl status ssh
```
Explicación:
Muestra el estado del servicio SSH. Debe aparecer como active (running).

## Notas importantes

- Si el firewall está activo, asegúrate de permitir el puerto 22:
```
sudo ufw allow ssh
```
- Si el servicio no arranca, revisa el archivo de configuración:
```
sudo nano /etc/ssh/sshd_config
```
y asegúrate de que Port 22 esté habilitado.

