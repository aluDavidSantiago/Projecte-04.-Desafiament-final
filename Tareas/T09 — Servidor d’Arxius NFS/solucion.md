## Introducción

En este proyecto, implementaremos un servidor NFS (Network File System) y un cliente Linux para resolver un problema crítico de gestión de archivos en la empresa **DevOptimize Solutions**, una startup dedicada al desarrollo de software en entornos Linux. Actualmente, cada desarrollador mantiene copias locales del código fuente y documentos, lo que provoca errores de versión y pérdida de eficiencia.

La solución propuesta consiste en centralizar los recursos compartidos mediante NFS, una tecnología nativa en sistemas Linux que permite compartir directorios a través de la red de forma rápida y eficiente. Esta prueba de concepto mostrará cómo configurar un servidor NFS y un cliente que acceda a los recursos, simulando el entorno del cliente con usuarios, grupos y permisos adecuados.

Durante el proyecto se abordarán los siguientes aspectos:

- **Preparación del entorno:** instalación y configuración de dos máquinas virtuales Linux (Ubuntu Server 24.04 LTS para el servidor y Zorin OS 18 para el cliente).
- **Configuración del servidor NFS:** creación de usuarios, grupos y directorios, asignación de permisos y configuración de exportaciones.
- **Pruebas funcionales:** demostración del comportamiento de opciones como `root_squash` y `no_root_squash`, así como restricciones de acceso según IP.
- **Mecanismos de montaje automático:** configuración en el cliente mediante `/etc/fstab`.
- **Conclusión y recomendaciones:** análisis de limitaciones y propuestas de mejora para una solución más segura y escalable.

Este documento servirá como guía detallada para reproducir la configuración y comprender las implicaciones técnicas de cada decisión.

## FASE 0: Preparación del entorno y configuración de red

### Introducción

Antes de instalar y configurar NFS, debemos asegurarnos de que las dos máquinas virtuales (Servidor y Cliente) estén correctamente configuradas, puedan comunicarse entre sí y tengan acceso a Internet. Esto incluye la creación de las máquinas, la configuración de adaptadores de red y la verificación de conectividad.

### Objetivo

### 1. Crear dos máquinas virtuales Linux:

- Servidor NFS: Ubuntu Server 24.04 LTS
- Cliente: Zorin OS 18


### 2. Configurar dos interfaces de red en cada máquina:

- Adaptador NAT: para acceso a Internet.
- Adaptador Host-Only: para comunicación directa entre servidor y cliente.

### 3. Verificar conectividad y asignar IPs estáticas o comprobar las dinámicas.

## Pasos detallados

### 1. Crear las máquinas virtuales

En VirtualBox (o similar), crea dos VMs:

- Servidor NFS: Ubuntu Server 24.04 LTS (instalar con idioma Español y habilitar SSH).
- Cliente: Zorin OS 18 (instalar con idioma Español).

### 2. Asigna recursos mínimos recomendados:

- Servidor: 2 GB RAM, 20 GB disco.
- Cliente: 2 GB RAM, 20 GB disco.

### 3. Configurar adaptadores de red

Para cada VM:

- Adaptador 1 (NAT): permite acceso a Internet.
- Adaptador 2 (Host-Only): permite comunicación entre las dos máquinas.

En VirtualBox:

- Ve a Configuración → Red → Adaptador 1 → NAT.
- Ve a Adaptador 2 → Activar → Red solo-anfitrión.

### 4. Verificar interfaces en cada máquina

En cada VM, ejecuta:
```
ip a
```
Explicación: Muestra todas las interfaces de red y sus direcciones IP. Debes identificar:

- Una IP del tipo 10.x.x.x o similar (NAT).
- Una IP del tipo 192.168.56.x (Host-Only).

### 5. Comprobar conectividad entre servidor y cliente

Desde el cliente (Zorin):
```
ping 192.168.56.108
```
Explicación: Comprueba que el cliente puede alcanzar la IP del servidor por la red Host-Only.

Desde el servidor:
```
ping 192.168.56.111
```
Explicación: Verifica la comunicación bidireccional.

### 6. Actualizar ambos sistemas
En cada máquina:
```
sudo apt update && sudo apt upgrade -y
```
Explicación: Actualiza la lista de paquetes y aplica las últimas actualizaciones para garantizar estabilidad y seguridad.

### Notas importantes

- Asegúrate de que las IPs 192.168.56.108 (Servidor) y 192.168.56.111 (Cliente) sean correctas. Si son dinámicas,
- considera configurarlas como estáticas en /etc/netplan/ (Ubuntu) y en la configuración de red de Zorin.
- Comprueba que el firewall no bloquee ICMP (ping) ni puertos NFS.
- Habilitar SSH en el servidor durante la instalación facilita la administración remota.

## FASE 1: Configuración del servidor NFS (IP: 192.168.56.108)

### Introducción
En esta fase instalaremos y configuraremos el servidor NFS en la máquina Ubuntu Server. Crearemos el directorio compartido, asignaremos permisos iniciales y configuraremos la exportación para que el cliente pueda montar el recurso.

### Objetivo

Instalar el servicio NFS.
Crear el directorio compartido /srv/nfs/projecte04.
Configurar permisos básicos.
Definir la exportación en /etc/exports.


### Pasos detallados

### 1. Actualizar paquetes del sistema
```
sudo apt update && sudo apt upgrade -y
```
Explicación por partes:

sudo: Ejecuta el comando con privilegios de superusuario.
apt: Gestor de paquetes en sistemas Debian/Ubuntu.
update: Actualiza la lista de paquetes disponibles desde los repositorios.
&&: Operador lógico que ejecuta el siguiente comando solo si el anterior fue exitoso.
upgrade: Instala las actualizaciones disponibles para los paquetes instalados.
-y: Responde automáticamente “sí” a las confirmaciones.

Por qué: Garantiza que el sistema esté actualizado y estable antes de instalar NFS.


### 2. Instalar el servicio NFS
```
sudo apt install nfs-kernel-server -y
```
Explicación por partes:

install: Orden para instalar paquetes.
nfs-kernel-server: Paquete que implementa el servidor NFS.
-y: Acepta la instalación sin pedir confirmación.

Por qué: Este paquete es el núcleo del servicio NFS en Linux.

### 3. Crear el directorio compartido
```
sudo mkdir -p /srv/nfs/projecte04
```
Explicación por partes:

mkdir: Crea directorios.
-p: Crea la ruta completa si no existe (incluyendo carpetas intermedias).
/srv/nfs/projecte04: Ruta donde se alojarán los archivos compartidos.

Por qué: Definimos un directorio estándar para recursos NFS.


### 4. Asignar permisos iniciales
```
sudo chown nobody:nogroup /srv/nfs/projecte04
```
```
sudo chmod 777 /srv/nfs/projecte04
```

Explicación:

chown nobody:nogroup: Cambia el propietario del directorio al usuario y grupo “nobody”, típico en NFS para acceso genérico.
chmod 777: Permite lectura, escritura y ejecución para todos los usuarios.

Por qué: Garantiza que cualquier cliente pueda acceder durante la prueba inicial.


### 5. Configurar exportación en /etc/exports
```
sudo nano /etc/exports
```

Añadir la línea:
```
/srv/nfs/projecte04 192.168.56.111(rw,sync,no_subtree_check)
```
Explicación de opciones:

rw: Permite lectura y escritura.
sync: Sincroniza cambios en disco antes de responder al cliente (más seguro).
no_subtree_check: Evita comprobaciones adicionales en subdirectorios (mejora rendimiento).

Por qué: Define qué directorio se comparte, con qué cliente y bajo qué condiciones.

### 6. Aplicar configuración y reiniciar servicio
```
sudo exportfs -a
```
```
sudo systemctl restart nfs-kernel-server
```

Explicación:

exportfs -a: Aplica todas las exportaciones definidas en /etc/exports.
systemctl restart nfs-kernel-server: Reinicia el servicio NFS para aplicar cambios.

Por qué: Necesario para que la configuración entre en vigor.

### 7. Verificar exportación
```
sudo exportfs -v
```
Explicación:

-v: Muestra detalles de las exportaciones activas.

Por qué: Confirma que el directorio está compartido correctamente.

## Notas importantes

- El firewall debe permitir tráfico NFS. Si usas ufw, añade:

```
sudo ufw allow from 192.168.56.111 to any port nfs
```

- Comprueba que el servicio está activo:
```
systemctl status nfs-kernel-server
```

- El directorio /srv/nfs/projecte04 tiene permisos abiertos para pruebas. Más adelante ajustaremos la seguridad.


## FASE 2: Configuración del cliente Zorin (IP: 192.168.56.111)

### Introducción
En esta fase configuraremos el cliente Zorin para que pueda montar el recurso compartido del servidor NFS. Instalaremos los paquetes necesarios, crearemos el punto de montaje y realizaremos pruebas de conectividad y montaje manual.

## Objetivo

Instalar soporte NFS en el cliente.
Crear el punto de montaje /mnt/projecte04.
Verificar conectividad con el servidor.
Montar el recurso NFS manualmente.


Pasos detallados
### 1. Actualizar paquetes del sistema
```
sudo apt update && sudo apt upgrade -y
```
Explicación por partes:

sudo: Ejecuta el comando con privilegios de superusuario.
apt: Gestor de paquetes en sistemas Debian/Ubuntu.
update: Actualiza la lista de paquetes disponibles.
&&: Ejecuta el siguiente comando solo si el anterior fue exitoso.
upgrade: Instala las actualizaciones disponibles.
-y: Responde automáticamente “sí” a las confirmaciones.

Por qué: Mantener el sistema actualizado antes de instalar NFS.

### 2. Instalar soporte NFS
```
sudo apt install nfs-common -y
```

Explicación por partes:

install: Orden para instalar paquetes.
nfs-common: Paquete que contiene las herramientas necesarias para clientes NFS.
-y: Acepta la instalación sin pedir confirmación.

Por qué: Permite que el cliente se comunique con el servidor NFS.

### 3 . Crear el punto de montaje
```
sudo mkdir -p /mnt/projecte04
```

Explicación por partes:

mkdir: Crea directorios.
-p: Crea la ruta completa si no existe.
/mnt/projecte04: Directorio donde se montará el recurso NFS.

Por qué: Necesitamos un punto de montaje para acceder al recurso compartido.


### 4. Comprobar conectividad con el servidor
```
ping 192.168.56.108
```

Explicación:

ping: Envía paquetes ICMP para comprobar conectividad.
192.168.56.108: IP del servidor NFS.

Por qué: Verifica que el cliente puede comunicarse con el servidor.

### 5. Ver exportaciones del servidor
```
showmount -e 192.168.56.108
```

Explicación por partes:

showmount: Muestra los directorios exportados por el servidor NFS.
-e: Lista las exportaciones activas.
192.168.56.108: IP del servidor.

Por qué: Confirma que el recurso /srv/nfs/projecte04 está disponible.

### 6. Montar el recurso NFS manualmente
```
sudo mount 192.168.56.108:/srv/nfs/projecte04 /mnt/projecte04
```

Explicación por partes:

mount: Comando para montar sistemas de archivos.
192.168.56.108:/srv/nfs/projecte04: Ruta del recurso NFS en el servidor.
/mnt/projecte04: Punto de montaje en el cliente.

Por qué: Permite acceder al recurso compartido desde el cliente.

### 7. Verificar montaje
```
df -h | grep projecte04
```
```
mount | grep nfs
```

Explicación:

df -h: Muestra el uso de disco en formato legible.
grep projecte04: Filtra la salida para mostrar solo el punto de montaje.
mount | grep nfs: Confirma que el recurso está montado como NFS.

Por qué: Verifica que el montaje se realizó correctamente.

## Notas importantes

- Si el montaje falla, revisa conectividad, permisos y configuración en /etc/exports del servidor.
- El recurso montado se perderá al reiniciar el cliente; en la siguiente fase configuraremos el montaje automático.
- Comprueba que el firewall del servidor permite tráfico NFS.


## FASE 3: Montaje automático en /etc/fstab

### Introducción
En esta fase configuraremos el montaje automático del recurso NFS en el cliente Zorin. Esto garantiza que el directorio compartido se monte de forma automática cada vez que el sistema se reinicie, evitando que los usuarios tengan que hacerlo manualmente.

## Objetivo

Editar el archivo /etc/fstab para añadir la entrada del recurso NFS.
Probar la configuración sin reiniciar.
Verificar el montaje tras reiniciar el sistema.

### Pasos detallados
### 1. Editar el archivo /etc/fstab
```
sudo nano /etc/fstab
```
Explicación por partes:

sudo: Ejecuta el comando con privilegios de superusuario.
nano: Editor de texto en terminal.
/etc/fstab: Archivo que define los sistemas de archivos que se montan automáticamente al inicio.

Por qué: Necesitamos añadir la configuración del recurso NFS para el montaje automático.

### 2. Añadir la línea al final del archivo
```
192.168.56.108:/srv/nfs/projecte04   /mnt/projecte04   nfs   defaults   0   0
```
Explicación por partes:

192.168.56.108:/srv/nfs/projecte04: Ruta del recurso NFS en el servidor.
/mnt/projecte04: Punto de montaje en el cliente.
nfs: Tipo de sistema de archivos.
defaults: Opciones por defecto (lectura/escritura, sincronización, etc.).
0 0: Parámetros para chequeo de errores y copias de seguridad (no aplican en NFS).

Por qué: Esta línea indica al sistema que debe montar el recurso NFS automáticamente al arrancar.

### 3. Probar la configuración sin reiniciar
```
sudo mount -a
```
Explicación por partes:

mount: Comando para montar sistemas de archivos.
-a: Monta todos los sistemas definidos en /etc/fstab.

Por qué: Permite verificar que la entrada añadida es correcta antes de reiniciar.

### 4. Verificar el montaje
```
df -h | grep projecte04
```
Explicación por partes:

df -h: Muestra el uso de disco en formato legible.
grep projecte04: Filtra la salida para mostrar solo el punto de montaje.

Por qué: Confirma que el recurso NFS está montado correctamente.

### 5. Reiniciar el cliente y comprobar
```
sudo reboot
```
Después del reinicio:
```
df -h | grep projecte04
```
Por qué: Verifica que el montaje automático funciona tras reiniciar el sistema.

## Notas importantes

- Si el montaje falla tras reiniciar, revisa la sintaxis en /etc/fstab (espacios y tabulaciones son críticos).
- Asegúrate de que el servidor NFS esté activo antes de reiniciar el cliente.
- Para entornos productivos, considera opciones adicionales en /etc/fstab como soft, hard, timeo, para mejorar tolerancia a fallos.

## FASE 4: Prueba de escritura y verificación

### Introducción
En esta fase realizaremos pruebas para confirmar que el cliente puede escribir en el recurso NFS montado y que los cambios se reflejan correctamente en el servidor. Esto valida la funcionalidad básica del servicio NFS y la correcta configuración de permisos.

## Objetivo

Crear un archivo de prueba en el cliente.
Escribir contenido en el archivo.
Verificar el contenido desde el cliente y el servidor.


### Pasos detallados
### 1. Crear un archivo de prueba en el cliente
```
sudo touch /mnt/projecte04/test.txt
```
Explicación por partes:

sudo: Ejecuta el comando con privilegios de superusuario.
touch: Crea un archivo vacío.
/mnt/projecte04/test.txt: Ruta del archivo dentro del punto de montaje NFS.

Por qué: Comprobamos que el cliente tiene permisos para crear archivos en el recurso compartido.

### 2. Escribir contenido en el archivo
```
echo "Prueba NFS OK" | sudo tee /mnt/projecte04/test.txt
```
Explicación por partes:

echo "Prueba NFS OK": Genera el texto que queremos escribir.
|: Redirige la salida del comando anterior al siguiente.
sudo tee /mnt/projecte04/test.txt: Escribe el contenido en el archivo con privilegios.

Por qué: Validamos que el cliente puede escribir datos en el recurso NFS.

### 3. Verificar el contenido desde el cliente
```
cat /mnt/projecte04/test.txt
```
Explicación por partes:

cat: Muestra el contenido de un archivo.
/mnt/projecte04/test.txt: Archivo creado en el recurso NFS.

Por qué: Confirmamos que el contenido se escribió correctamente.

### 4. Comprobar el archivo desde el servidor
```
ls -l /srv/nfs/projecte04
```
```
cat /srv/nfs/projecte04/test.txt
```
Explicación por partes:

ls -l: Lista los archivos con detalles (permisos, propietario, tamaño).
/srv/nfs/projecte04: Directorio compartido en el servidor.
cat /srv/nfs/projecte04/test.txt: Muestra el contenido del archivo desde el servidor.

Por qué: Verificamos que el archivo creado en el cliente existe en el servidor y contiene el texto esperado.

## Notas importantes

- Si el archivo no aparece en el servidor, revisa el montaje en el cliente (mount | grep nfs).
- Si el contenido no coincide, comprueba permisos y opciones en /etc/exports.
- Recuerda que los permisos actuales son abiertos (777); en la siguiente fase ajustaremos la seguridad.

## FASE 5: Seguridad y optimización

###Introducción
En esta fase ajustaremos la configuración del servidor NFS para mejorar la seguridad y optimizar el comportamiento del servicio. Hasta ahora hemos trabajado con permisos abiertos (777), lo cual no es adecuado para entornos reales. También explicaremos y aplicaremos opciones avanzadas como no_root_squash para controlar el acceso del usuario root desde el cliente.

## Objetivo

Ajustar permisos del directorio compartido.
Configurar opciones avanzadas en /etc/exports.
Reaplicar la configuración y reiniciar el servicio.


### Pasos detallados

### 1.Ajustar permisos del directorio compartido
```
sudo chmod 755 /srv/nfs/projecte04
```
```
sudo chown root:root /srv/nfs/projecte04
```
Explicación por partes:

chmod 755: Permite lectura y ejecución para todos, pero escritura solo para el propietario (root).
chown root:root: Establece root como propietario y grupo del directorio.

Por qué: Limita el acceso para evitar modificaciones por usuarios no autorizados.

### 2. Editar el archivo /etc/exports para opciones avanzadas
```
sudo nano /etc/exports
```
Añadir o modificar la línea:
```
/srv/nfs/projecte04 192.168.56.111(rw,sync,no_subtree_check,no_root_squash)
```
Explicación de opciones:

rw: Permite lectura y escritura.
sync: Sincroniza cambios en disco antes de responder al cliente.
no_subtree_check: Mejora rendimiento evitando comprobaciones en subdirectorios.
no_root_squash: Permite que el usuario root del cliente mantenga privilegios en el recurso NFS.

Por qué: Por defecto, NFS aplica root_squash (convierte root en nobody para seguridad). Aquí lo desactivamos para permitir tareas administrativas desde el cliente.

### 3. Reaplicar configuración y reiniciar servicio
```
sudo exportfs -a
```
```
sudo systemctl restart nfs-kernel-server
```
Explicación por partes:

exportfs -a: Aplica todas las exportaciones definidas en /etc/exports.
systemctl restart nfs-kernel-server: Reinicia el servicio NFS para aplicar cambios.

Por qué: Necesario para que las nuevas opciones entren en vigor.

### 4. Verificar exportaciones activas
```
sudo exportfs -v
```
Explicación:

-v: Muestra detalles de las exportaciones y sus opciones.

Por qué: Confirma que no_root_squash y demás opciones están activas.

## Notas importantes

- Seguridad: Usar no_root_squash implica riesgos, ya que el root del cliente tiene control total sobre el recurso. Solo debe aplicarse en entornos controlados.
- Alternativa segura: Mantener root_squash y gestionar permisos mediante usuarios/grupos con UID/GID coincidentes en servidor y cliente.
- Recomendación: Para producción, implementar autenticación centralizada (LDAP, Kerberos) y cifrado (NFS sobre TLS o VPN).

# CONCLUSIÓN Y RECOMENDACIONES FINALES

### Introducción

En esta sección analizaremos los resultados obtenidos en la prueba de concepto y propondremos mejoras para que la solución sea más segura, escalable y adecuada para un entorno productivo. Aunque NFS es una tecnología eficiente para compartir archivos en sistemas Linux, su configuración básica presenta limitaciones importantes.

### Análisis de la solución implementada

- Objetivo cumplido: Se ha configurado un servidor NFS y un cliente Linux que comparten un directorio de forma funcional.
- Pruebas realizadas: Montaje manual y automático, verificación de escritura, ajuste de permisos y opciones avanzadas (no_root_squash).
- Limitaciones detectadas:

- - Falta de autenticación centralizada: NFS confía en UID/GID, lo que puede generar inconsistencias si no se sincronizan entre sistemas.
- - Seguridad débil: El uso de no_root_squash otorga privilegios excesivos al cliente.
- - Tráfico sin cifrar: NFS transmite datos en texto plano, lo que es vulnerable en redes inseguras.

## Recomendaciones para mejorar la solución

### Autenticación centralizada:

- Implementar un servicio como LDAP o Kerberos para gestionar usuarios y permisos de forma unificada.
- Esto evita la necesidad de replicar manualmente usuarios y grupos en cada máquina.

### Seguridad en el transporte:

- Configurar NFS sobre TLS o utilizar una VPN para cifrar el tráfico entre cliente y servidor.
- Alternativamente, considerar SSHFS para entornos donde la seguridad sea prioritaria.

### Gestión avanzada de permisos:

- Mantener root_squash activado para evitar privilegios excesivos.
- Definir permisos granulares mediante ACLs (Access Control Lists) en el sistema de archivos.

### Alta disponibilidad y rendimiento:

- Para entornos con múltiples clientes, considerar NFSv4 en lugar de NFSv3, ya que ofrece mejoras en seguridad y rendimiento.
- Implementar mecanismos de redundancia (RAID, replicación) en el servidor para evitar pérdida de datos.

### Monitorización y alertas:

- Configurar herramientas como Nagios o Prometheus para supervisar el estado del servicio NFS y el uso de recursos.

## Conclusión final
La solución implementada cumple con los requisitos del cliente para una prueba de concepto, pero no es adecuada para producción sin mejoras significativas en seguridad, autenticación y gestión de permisos. NFS sigue siendo una opción eficiente en entornos Linux, pero debe complementarse con mecanismos adicionales para garantizar integridad, confidencialidad y disponibilidad.
