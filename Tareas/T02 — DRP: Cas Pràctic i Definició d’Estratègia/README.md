# 🖥️ T02: DPR – Copias de Seguridad (Cas Práctico)

---

## 🟦 Breve Descripción

Continuación de la política de copias de seguridad para el cliente **"Muntatges i Serveis Tècnics SL"**.  
El objetivo ahora es **llevarlo a la práctica**, creando guías técnicas y pruebas de concepto para que el personal pueda implementar el plan de copias.

---

## 🟩 Parte 1: Copia de Seguridad de Equipos Windows

- **Excepción:** Se realizará copia del equipo Windows del director (datos importantes que no se suben al servidor).  
- **Esquema 3-2-1:**  
  - Copia local en disco secundario del equipo  
  - Copia en la nube: Google Drive usando **[Duplicati](https://duplicati.com/)**

### Procedimiento Práctico

1. Crear una **máquina virtual Windows 11** con dos discos (sistema + 10 GB para backups).  
2. Instalar Duplicati y configurar planes de copia:  
   - Copia cada hora al disco secundario  
   - Copia diaria a Google Drive a las 18:00  
3. Añadir archivos en la carpeta `Documentos`.  
4. Borrar y **restaurar** desde el disco secundario y desde la copia en la nube.  

---

## 🟧 Parte 2: Copia de Seguridad del Servidor Linux

- **Herramienta:** Duplicity  
- Permite backups locales o remotos, y se puede combinar con **cron** para automatización.

### Procedimiento Práctico

1. Crear **máquina virtual Ubuntu Server** con segundo disco de 10 GB.  
2. Inicializar y formatear el disco en **XFS**, montarlo en `/media/backup`.  
3. Instalar **Duplicity**.  
4. Crear usuarios adicionales y archivos de prueba (4 archivos de 10 MB en `/home`).  
5. Realizar copia completa de `/home` y comprobar restauración.  
6. Añadir un nuevo archivo de 4 MB → observar copia incremental.  
7. Desmontar unidad de backup al finalizar.

### Automatización con Scripts y Cron

1. **Script fullbackup.sh**: copia completa de `/home` usando variable `PASSPHRASE`.  
   - Dar permisos de ejecución.  
   - Programar en **cron** los domingos a las 23:00.  
2. **Script incrementalbackup.sh**: copias incrementales de lunes a sábado a las 23:00.  
   - Igualmente con variable `PASSPHRASE`.  
   - Dar permisos y programar en cron como root.  

---

## 📚 Materiales y Recursos

- **[Duplicati](https://duplicati.com/)**  
- **[WayToIT – Crear archivos con fsutil (Windows)](https://waytoit.wordpress.com/2015/03/15/creando-archivos-con-fsutil/)**  
- **[WayToIT – Crear archivos de prueba en Linux](https://waytoit.wordpress.com/2015/03/21/creando-archivos-de-prueba-en-linux/)**  
- **[Duplicity man page](http://manpages.ubuntu.com/manpages/trusty/man1/duplicity.1.html)**  
- **[Programar tareas en Linux con cron](https://geekytheory.com/programar-tareas-en-linux-usando-crontab)**

