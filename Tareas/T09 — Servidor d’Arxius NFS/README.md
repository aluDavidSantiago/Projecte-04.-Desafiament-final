# Introducción

Muy bien, equipo de consultores junior. En nuestro proyecto, nos enfrentamos a un requisito técnico muy habitual por parte de nuestros clientes: la **centralización de datos en entornos Linux**.

---

## 🟦 El Caso del Cliente: DevOptimize Solutions

Nuestro cliente, **DevOptimize Solutions**, es una pequeña startup de desarrollo de software que trabaja exclusivamente con Linux.  

Actualmente enfrentan un problema crítico: su **código fuente y activos** (documentos de diseño, scripts) están desorganizados. Cada desarrollador mantiene copias locales, lo que provoca:

- Errores de versión constantes.  
- Pérdida de eficiencia significativa.  

**Encargo del cliente:** Implementar un **servidor de archivos centralizado**.  

Dado que todo el entorno es Linux, la **solución nativa más rápida y eficiente** es **NFS (Network File System)**.  

> Nota: El cliente no utiliza un entorno de autenticación centralizada y, por ahora, no planea implementarlo.

---

## 🟩 Objetivo de la Demostración

Para mostrar al cliente cómo funcionará la solución y las limitaciones actuales, se te solicita realizar una **demostración del sistema**:

- Crear un **servidor NFS (NFSv3)**.  
- Crear un **cliente Linux** que consuma los recursos compartidos.  
- Crear **usuarios y grupos** para simular el entorno real del cliente.  
- Demostrar el **control de acceso** usando:  
  - Opciones de exportación en `/etc/exports`.  
  - Permisos del sistema de archivos (`chmod`, `chown`).

---

## 🟧 Recursos y Material de Apoyo

- **Material propio:** UD5. AA1. NFS. Disponible en Moodle del módulo de *Sistemas Operativos en Red*.  
- Ruiz, P. (2021, noviembre 22). *NFS (parte 1): Instalación en un servidor Ubuntu 20.04 LTS*. SomeBooks.es.  
  [Enlace](https://somebooks.es/nfs-parte-1-instalacion-en-un-servidor-ubuntu-20-04-lts/)  
- Ruiz, P. (2021, diciembre 2). *NFS (parte 2): Instalación en un cliente Ubuntu 20.04 LTS*. SomeBooks.es.  
  [Enlace](https://somebooks.es/nfs-parte-2-instalacion-en-un-cliente-ubuntu-20-04-lts/)  
- *Network File System (NFS)*. Ubuntu Server.  
  [Enlace](https://documentation.ubuntu.com/server/how-to/networking/install-nfs/)  

---
