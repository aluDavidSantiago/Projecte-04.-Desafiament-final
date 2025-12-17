# Guía Técnica: Copias de Seguridad en Windows 11 con Duplicati

## FASE 0: Creación de la máquina virtual

### Introducción: 
Se crea una VM con Windows 11 y dos discos: uno para el sistema y otro para copias.

### Objetivo:

Disco 1: Sistema operativo (Windows 11).
Disco 2: 10 GB para copias de seguridad.

### Pasos:

### 1. Crear VM en tu hipervisor (VirtualBox, VMware, Hyper-V).

Asignar:

- Disco principal: tamaño recomendado (mínimo 64 GB).
- Disco secundario: 10 GB.


### 2. Instalar Windows 11 normalmente.

### Notas:

- Asegúrate de que el disco secundario aparece en el sistema antes de continuar.


### FASE 1: Configuración del disco secundario

### Introducción: 
Inicializamos y formateamos el disco para usarlo como destino de copias.

### Objetivo:

- Inicializar disco en GPT.
- Crear volumen NTFS llamado BACKUP.

### Pasos:
### 1. Abrir Administrador de discos

Win + X → Administración de discos.

<img src="IMGW/1.png" alt="..." width="600" height="auto">

### 2. Inicializar disco

Aparecerá ventana Inicializar disco.
Seleccionar GPT → Aceptar.

### 3. Crear volumen simple

Asistente → Nuevo volumen simple.

### Formato:

- Sistema de archivos: NTFS.
- Tamaño: predeterminado.
- Nombre: BACKUP.
- Activar Formato rápido.

Finalizar.

<img src="IMGW/2.png" alt="..." width="600" height="auto">

### Notas:

- El disco aparecerá como unidad E: (o similar).


### FASE 2: Instalación de Duplicati

### Introducción: 
Instalamos la herramienta para gestionar copias.

### Pasos:

<img src="IMGW/3.png" alt="..." width="600" height="auto">

1. Descargar desde https://duplicati.com/download.
2. Instalar con opciones por defecto.
3. Al finalizar, se abrirá en el navegador la interfaz web.

<img src="IMGW/5.png" alt="..." width="600" height="auto">

### Nota: 
Pedirá contraseña. Para la práctica, dejar en blanco (en producción, usar contraseña segura).


### FASE 3: Preparación de datos para la copia

### Introducción:
- Creamos archivos de prueba en la carpeta Documentos.

<img src="IMGW/6.png" alt="..." width="600" height="auto">

- Comando en PowerShell:
```
# Crear carpeta y archivos de prueba
$docs = "$env:USERPROFILE\Documents"
New-Item -Path $docs\Test -ItemType Directory -Force | Out-Null

# Crear 5 archivos binarios de 5MB
1..5 | ForEach-Object { fsutil file createnew "$docs\Test\file$_.bin" 5242880 }

# Crear archivo de texto
Set-Content "$docs\notas.txt" "Notas del director"
```

### Explicación:

$env:USERPROFILE\Documents: ruta del perfil del usuario.
fsutil file createnew: crea archivos vacíos del tamaño indicado (5242880 bytes ≈ 5 MB).

### Notas:

- Verifica que los archivos se han creado en Documentos.


### FASE 4: Configuración de copia local

### Introducción: 
Creamos un plan de copia hacia el disco secundario.
### Pasos:

1. En la interfaz de Duplicati → Añadir copia de seguridad.

<img src="IMGW/7.png" alt="..." width="600" height="auto">

General:

- Nombre: Backup_Local.
- Cifrado: AES-256.
- Contraseña: (recomendado).

<img src="IMGW/9.png" alt="..." width="600" height="auto">

Destino:

- Tipo: Carpeta local.
- Ruta: E:\ (disco BACKUP).

<img src="IMGW/10.png" alt="..." width="600" height="auto">


Origen:

- Seleccionar carpeta Documentos.

<img src="IMGW/11.png" alt="..." width="600" height="auto">

Horario:

- Cada 1 hora, todos los días.

<img src="IMGW/12.png" alt="..." width="600" height="auto">

- y en opciones no tocamos nada

<img src="IMGW/13.png" alt="..." width="600" height="auto">

Guardar y ejecutar copia.

<img src="IMGW/14.png" alt="..." width="600" height="auto">

### Notas:

Verifica en unidad E: que se crean archivos cifrados.

<img src="IMGW/16.png" alt="..." width="600" height="auto">

### FASE 5: Configuración de copia en Google Drive

### Introducción: 
Configuramos copia en la nube.

<img src="IMGW/18.png" alt="..." width="600" height="auto">

### Pasos:

1. Añadir nueva copia → Destino: Google Drive.
Obtener AuthID:

<img src="IMGW/19.png" alt="..." width="600" height="auto">

- Automático (preferido) o manual si falla.


2. Ruta en Drive:

3. Crear carpeta BackupDuplicati.

<img src="IMGW/20.png" alt="..." width="600" height="auto">

4. Origen: Documentos.

<img src="IMGW/21.png" alt="..." width="600" height="auto">

5. Horario:

- Cada día a las 18:00.

<img src="IMGW/22.png" alt="..." width="600" height="auto">

6. Guardar y ejecutar copia.

<img src="IMGW/25.png" alt="..." width="600" height="auto">

### Notas:

- Verifica que la carpeta aparece en Google Drive.

### FASE 6: Restauración desde disco local

### Pasos:

1. Eliminar contenido de Documentos.

  <img src="IMGW/27.png" alt="..." width="600" height="auto">

2. En Duplicati → Restaurar.

 <img src="IMGW/29.png" alt="..." width="600" height="auto">

3. Seleccionar copia local → elegir archivos → restaurar en ubicación original.

<img src="IMGW/33.png" alt="..." width="600" height="auto">

<img src="IMGW/30.png" alt="..." width="600" height="auto">

<img src="IMGW/31.png" alt="..." width="600" height="auto">

<img src="IMGW/34.png" alt="..." width="600" height="auto">


4. Comprobar que los archivos vuelven a aparecer.
   
<img src="IMGW/35.png" alt="..." width="600" height="auto">

### FASE 7: Restauración desde Google Drive

### Pasos:

- Igual que la anterior, pero seleccionando copia en Google Drive.

<img src="IMGW/36.png" alt="..." width="600" height="auto">
<img src="IMGW/37.png" alt="..." width="600" height="auto">
<img src="IMGW/38.png" alt="..." width="600" height="auto">
<img src="IMGW/39.png" alt="..." width="600" height="auto">

### Notas finales importantes

- Seguridad: Siempre usar cifrado y contraseña en entornos reales.
- Pruebas: Verificar integridad tras restauración.
- Esquema 3-2-1: Este ejercicio cubre 2 copias (local + nube).

