# Fase 1: Trabajo individual

## 1. Qué copiar (Priorización)

Las datos más críticos del servidor son la **base de datos** y los **documentos de proyectos**, ya que representan los activos más valiosos de la empresa.  
La base de datos contiene información sensible de clientes y operaciones, mientras que los proyectos representan trabajo acumulado.

Respecto a los **equipos cliente**, sí realizaría una copia de seguridad, pero **solo de los documentos locales que no estén sincronizados con el servidor**. Aunque la mayor parte de la información se guarda en el servidor, siempre puede existir algún archivo crítico creado en local y no subido aún.  
Por tanto, se recomienda una **copia selectiva de los clientes**.

## 2. Periodicidad y Tipo de Copia

- **Servidor completo:** Copia completa **semanal**, para disponer de una imagen íntegra reciente.  
- **Base de datos:** Copia **diaria incremental**, debido a su constante cambio y para optimizar tiempo y espacio.  
- **Documentos de proyectos:** Copia **cada dos días**, de tipo **diferencial**, reduciendo el riesgo de pérdida sin saturar el sistema.  
- **Carpetas personales de usuarios:** Copia **semanal incremental**, ya que la información es útil pero no tan crítica.  
- **Equipos cliente:** Copia **mensual completa** de los documentos locales.

## 3. Medios y Ubicación (Regla 3-2-1)

Siguiendo la regla **3-2-1**:

- **NAS interno:** Para las copias diarias y semanales. Acceso rápido para restauraciones inmediatas.  
- **Cintas magnéticas:** Para copias completas mensuales, seguras y fuera de línea.  
- **Cloud externo:** Para copias críticas (base de datos y proyectos), garantizando una copia fuera de la empresa.

De esta manera:

- Las copias más recientes estarán en el **NAS**.  
- Las copias completas en las **cintas**.  
- Las copias críticas en la **nube**.

---

# Fase 2: Trabajo por parejas

## Esquema 3-2-1 de Copias (Propuesta Unificada)

| **Elemento**            | **Propuesta de la Pareja** | **Justificación** |
|------------------------|-----------------------------|-------------------|
| **Datos Críticos**     | Base de datos               | Es la más importante porque contiene datos personales e información clave de los clientes. |
| **Periodicidad (BD)**  | Diariamente                 | Una copia mensual podría causar grandes pérdidas; la copia diaria es segura y ligera. |
| **Tipo de Copia (BD)** | Diferencial                 | Permite restaurar el estado exacto de un día y es más eficiente que una copia completa diaria. |
| **Medio 1 (Local)**    | RDX                         | Rápido, seguro, fácil de sustituir y protegido físicamente. |
| **Medio 2 (Externo)**  | Cloud                       | Permite disponer de una copia fuera de la empresa en caso de desastre. |

---

# Fase 3: Trabajo en grupo

## 1) Datos objeto de copia

### 1.1 Servidor

#### **Datos críticos**
- Base de datos  
- Documentos de proyectos  
- Carpeta de documentos de los usuarios  

#### **Datos no críticos**
- Otras carpetas personales (imágenes, descargas, aplicaciones…)

---

### **Frecuencia de copia del servidor**

#### **Servidor completo**
- **Frecuencia:** Mensual  
- **Tipo:** Completa  

#### **Datos críticos**

**Base de datos**  
- Frecuencia: Diaria  
- Tipo: Diferencial  

**Carpeta de documentos de usuarios**  
- Frecuencia: Diaria  
- Tipo: Incremental  

**Documentos de proyectos**  
- Frecuencia: Semanal  
- Tipo: Completa  

#### **Datos no críticos**
- Frecuencia: Mensual  
- Tipo: Completa  

---

### **1.2 Equipos Cliente**

#### **Datos críticos (documentos, trabajos...)**
- Frecuencia: Semanal  
- Tipo: Incremental (solo Documentos)

#### **Datos no críticos**
- Frecuencia: Mensual  
- Tipo: Completa (si se realiza)

---

## 2) Cronograma Semanal Detallado

| **Día**        | **Datos**                                  | **Tipo de copia**                         | **Medio**            |
|----------------|---------------------------------------------|--------------------------------------------|------------------------|
| **Lunes**      | Base de datos + Carpeta de documentos       | Diferencial / Incremental                  | NAS (RDX)              |
| **Martes**     | Base de datos + Carpeta de documentos       | Diferencial / Incremental                  | NAS                    |
| **Miércoles**  | Base de datos + Carpeta de documentos       | Diferencial / Incremental                  | NAS                    |
| **Jueves**     | Base de datos + Carpeta de documentos       | Diferencial / Incremental                  | NAS                    |
| **Viernes**    | BD + Carpeta docs + Proyectos               | Diferencial / Incremental / Completa       | NAS                    |
| **Sábado**     | Replicación al Cloud                        | —                                          | Cloud                  |
| **Domingo**    | Copia mensual (datos no críticos)           | Completa                                   | Disco externo / cinta  |

---

## 3) Elección de Medios y Ubicación (Regla 3-2-1)

### **3.1 Medio 1 (Local)**

- **Tipo:** RDX / NAS  
- **Ubicación:** Sala de servidores  
- **Responsable:** Técnico de sistemas / Administrador de redes  

### **3.2 Medio 2 (Externo)**

- **Tipo:** Cloud  
- **Proveedor:** Google Cloud, Azure o AWS  
- **Frecuencia de actualización:** Diaria (réplica automática nocturna)  
- **Responsable:** Responsable IT  

### **3.3 Ubicación fuera de la empresa**

- **Ubicación:** Nube (región diferente)  
- **Datos almacenados:** Datos críticos (BD, documentos, proyectos)  
- **Seguridad:**  
  - Acceso cifrado  
  - Autenticación multifactor  
  - Políticas basadas en roles  
- **Responsable:** Responsable de copias y recuperación  

---

## 4) Estrategia de Recuperación (RTO / RPO)

### **Objetivos**

- **RPO:** 4 horas  
- **RTO:** 4 horas  

### **Cómo se garantiza**

- Uso de copias **diferenciales e incrementales**  
- Copias rápidas almacenadas en **NAS / RDX**

### **Procedimiento de recuperación**

1. Identificar el punto de restauración más reciente (según RPO).  
2. Cargar la copia diferencial o incremental desde el NAS.  
3. Verificar la integridad de la restauración.  
4. Restaurar servicios por prioridad (BD → proyectos → documentos).  
5. Validación final con el equipo técnico.  

### **Personal responsable**

- Administrador de sistemas  
- Técnico de soporte  
- Supervisor IT

