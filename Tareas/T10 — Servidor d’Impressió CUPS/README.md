# Introducción

Muy bien, equipo. En nuestra consultora **EverPia**, buscamos constantemente optimizar los recursos de nuestros clientes para reducir costos y simplificar la gestión.  

Uno de los puntos más caóticos en cualquier oficina es **la gestión de impresoras**:  

- Drivers incompatibles  
- Costos de tóner descontrolados  
- Equipos que no saben a qué impresora enviar la tarea  

La solución profesional es implementar un **Servidor de Impresión Centralizado**.

---

## 🟦 Caso del Cliente: DevOptimize Solutions

Nuestro cliente, **DevOptimize Solutions**, nos ha solicitado una propuesta para centralizar la impresión en todos sus departamentos.  
El entorno es mixto:

- Clientes Linux: Zorin OS  
- Servidores: Ubuntu Server

---

## 🟩 Vuestra Misión: Probar la Prueba de Concepto (PoC)

Antes de invertir en impresoras de red costosas, el cliente quiere **una PoC** que demuestre que un servidor Linux puede gestionar una impresora y compartirla de manera transparente con los clientes Zorin.  

Para simular la impresora sin comprar hardware, utilizaremos **cups-pdf**, una impresora virtual que "imprime" los documentos en formato PDF en lugar de papel.

**Objetivo:** Configurar el escenario y demostrar que un cliente puede enviar trabajos de impresión al servidor.

---

## 🟧 Escenario de Trabajo

Se utilizará el mismo escenario que en la PoC de NFS:

| Máquina | Sistema Operativo | Configuración de Red |
|---------|-----------------|--------------------|
| M1 (Servidor) | Ubuntu Server | 1 interfaz NAT + 1 Host-Only |
| M2 (Cliente) | Zorin OS (Desktop) | Misma configuración que el servidor |

---

## 🟨 Pasos de la PoC (Propuesta)

1. Instalar **CUPS** en el servidor.  
2. Instalar la **impresora virtual cups-pdf**.  
3. Configurar CUPS para administración y permitir que escuche por todas las interfaces.  
4. Usar el **interfaz web de CUPS** para compartir la impresora.  
5. En el cliente Zorin, **agregar la impresora** compartida.  
6. Realizar **pruebas de impresión** con varios documentos.  
7. Comprobar en el servidor los archivos PDF generados correspondientes a los trabajos impresos.

> Documentar todas las **comandos utilizados** e incluir **capturas de pantalla** para demostrar el correcto funcionamiento.

---

## 🟦 Materiales y Links de Apoyo

- Material propio: **UD5. AA1. CUPS**, disponible en Moodle del módulo de *Sistemas Operativos en Red*.  
- J.B. Alex Mantich. (2024, 15 febrero). *Instalación de servidor de impresión en CUPS para Linux* [Vídeo]. YouTube.  
  [Ver vídeo](https://www.youtube.com/watch?v=FNwSTrOSgZQ)  
- Canonical. *Network File System (NFS)*. Ubuntu Server Documentation.  
  [Enlace](https://documentation.ubuntu.com/server/how-to/networking/install-nfs/)  
- R00t. (2025, 25 abril). *How To Install CUPS Print Server on Ubuntu 24.04 LTS*. Idroot.  
  [Enlace](https://idroot.us/install-cups-print-server-ubuntu-24-04/)

