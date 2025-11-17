# 🛡️ Gestión de Copias de Seguridad – Resumen de la Actividad

---

## 🟦 Introducción

El responsable de seguridad presenta el tema de las copias de seguridad mediante material didáctico.  
Después, se trabajarán los contenidos mediante una dinámica cooperativa.

---

## 🧩 Caso del Cliente: "Muntatges i Serveis Tècnics SL"

### Infraestructura Técnica
- **Servidor Ubuntu Server** con:
  - Documentos de proyectos (300 GB)
  - Bases de datos de Contabilidad/Clientes (20 GB)
  - Carpetas personales (100 GB)
- **10 equipos clientes Windows 10/11**
- **Conexión a Internet:** 600 Mbps simétricos

### Requisitos de Recuperación
- **RTO:** menos de 4 horas  
- **RPO:**  
  - 24 h para la mayoría de datos  
  - 4 h para Contabilidad/Clientes  
- **Retención:** historial mínimo de 1 mes

---

# 🟩 Fase 1: Trabajo Individual

1. **Qué copiar:** priorizar datos críticos y decidir si copiar los equipos clientes.  
2. **Periodicidad y tipo de copia:** calendario semanal (diaria/semanal/mensual) y tipo (completa, diferencial, incremental).  
3. **Medios y ubicación:** elección del medio (NAS, discos, nube, cintas) siguiendo la regla **3-2-1**.

---

# 🟧 Fase 2: Trabajo por Parejas

1. Comparación y consenso de las respuestas.  
2. Elaboración de un esquema 3-2-1 conjunto.

| Elemento | Propuesta | Justificación |
|---------|-----------|---------------|
| Datos críticos |  |  |
| Periodicidad (BD) |  |  |
| Tipo de copia (BD) |  |  |
| Medio 1 (Local) |  |  |
| Medio 2 (Externo) |  |  |

---

# 🟪 Fase 3: Trabajo en Grupo

1. Debate de las diferentes propuestas (coste, tiempo, seguridad, simplicidad).  
2. Creación de la **Política Final de Copias de Seguridad**.

---

# 📄 Documento Final del Grupo

## 1) Datos objeto de copia  
Frecuencia y clasificación (servidor/clientes, críticos/no críticos).

## 2) Cronograma semanal

| Día | Datos | Tipo de copia | Medio |
|-----|--------|----------------|--------|
| Lunes | | | |
| Martes | | | |
| ... | | | |
| Domingo | | | |

## 3) Medios y ubicación (Regla 3-2-1)  
- Medio local  
- Medio externo  
- Ubicación fuera de las instalaciones y responsable

## 4) Estrategia de recuperación (RTO/RPO)  
Garantizar cumplimiento de RTO 4 h y RPO 4 h para Contabilidad/Clientes.

---

# 📚 Materiales de Apoyo

- **Moodle 0226 Seguridad Informática – RA2.AA3 Copias**  
- **[INCIBE – Guía de Copias de Seguridad](https://www.incibe.es/sites/default/files/contenidos/guias/guia-copias-de-seguridad.pdf)**  
- **[Xataka – Método 3-2-1 (YouTube)](https://youtu.be/PM_M4Iz6I4o?si=F7DRyDDTZE3hjWn8)**
