### **Manual de Despliegue Guiado: WordPress Modular en AWS**

**Bienvenida/o, futura/o Arquitecta/o de la Nube,**

Como especialista en AWS con un enfoque en metodologías de aprendizaje, he diseñado este manual para que no solo ejecutes comandos, sino para que comprendas el *porqué* de cada decisión. A través de un caso práctico, construirás una solución robusta, segura y modular, desarrollando habilidades críticas para tu carrera.

**Objetivos de Aprendizaje:**

*   Interpretar plantillas de Infraestructura como Código (IaC) para derivar requerimientos.
*   Diseñar una topología de red segura y eficiente en AWS.
*   Implementar una arquitectura multi-pila utilizando AWS CloudFormation.
*   Validar y documentar un despliegue en la nube.

---

#### **Caso Práctico: El Portafolio de Elena**

**La Cliente:** Elena es una fotógrafa profesional que necesita lanzar su portafolio online y su blog.
**El Desafío:** Necesita una solución que sea:
1.  **Económica:** Quiere empezar sin costos fijos, aprovechando la capa gratuita de AWS.
2.  **Segura:** Sus datos y los de sus clientes deben estar protegidos. La base de datos no debe ser accesible desde internet.
3.  **Confiable:** El sitio debe estar disponible y la base de datos debe ser gestionada para evitar problemas.
4.  **Escalable a Futuro:** La solución debe ser una base sólida que pueda crecer si su negocio despega.

Tu misión es construir esta solución para Elena.

**Prerrequisitos:**
*   Una cuenta de AWS activa.
*   Haber creado un **Par de Claves (Key Pair)** en la consola de EC2 de tu región preferida (ej. `us-east-1`).

---

### **Fase 1: Análisis (Tiempo Estimado: 20 minutos)**

**Objetivo:** Comprender la solución antes de construirla, traduciendo el código a requerimientos.

**Tu Tarea:** Abre los tres archivos YAML (`00-network-stack.yaml`, `01-security-stack.yaml`, `02-application-stack.yaml`) en un editor de texto. Léelos y, basándote en su contenido, extrae los siguientes requerimientos.

**1. Requerimientos Funcionales (¿Qué hace el sistema?)**
*   
    *   
    *   
    *   
    *   
    *   El sitio web es accesible al público a través de HTTP (puerto 80).

**2. Requerimientos No Funcionales (¿Cómo lo hace? - Calidad, Seguridad, Costo)**
*   **
    *   **Costo:** 
    *   **Seguridad:**
        *   
        *   
        *   
    *   **Modularidad:** 
    *   **Disponibilidad:** 

---

### **Fase 2: Diseño (Tiempo Estimado: 15 minutos)**

**Objetivo:** Visualizar la arquitectura que has analizado. Un buen arquitecto siempre tiene un diagrama.

**Tu Tarea:** Basándote en el análisis anterior, dibuja en un papel o en una herramienta de diagramación la topología de la solución. Tu diagrama debe mostrar: la VPC, las subredes (pública y privadas), la EC2, la RDS, el Internet Gateway y cómo se conectan los grupos de seguridad.

**Diagrama de Referencia para Autoevaluación:**


**Puntos Clave del Diseño:**
*   El tráfico de internet solo puede llegar a la subred pública.
*   La EC2 está en la subred pública para ser alcanzada por los usuarios.
*   La RDS está en las subredes privadas, protegida del exterior.
*   El Grupo de Seguridad `SG-RDS` solo acepta conexiones del Grupo de Seguridad `SG-EC2`.

---

### **Fase 3: Implementación (Tiempo Estimado: 25 minutos)**

**Objetivo:** Materializar tu diseño utilizando las plantillas de CloudFormation.

**Tu Tarea:** Sigue estos pasos en orden. ¡La secuencia es crucial!

1.  **Paso 3.1: Desplegar la Red**
    *   Ve a la consola de AWS -> **CloudFormation**.
    *   Crea una nueva pila usando el archivo `00-network-stack.yaml`.
    *   Nombra la pila: **`WordPress-Network`**. (¡El nombre exacto es importante!).
    *   Lanza la pila y espera a que el estado sea **`CREATE_COMPLETE`**.

2.  **Paso 3.2: Desplegar la Seguridad**
    *   Crea otra pila nueva con `01-security-stack.yaml`.
    *   Nombra la pila: **`WordPress-Security`**.
    *   En la pantalla de **Parámetros**, introduce lo siguiente:
        *   `NetworkStackName`: **`WordPress-Network`**
    *   Lanza la pila y espera a que el estado sea **`CREATE_COMPLETE`**.

3.  **Paso 3.3: Desplegar la Aplicación**
    *   Crea la última pila con `02-application-stack.yaml`.
    *   Nombra la pila: **`WordPress-Application`**.
    *   En la pantalla de **Parámetros**, introduce:
        *   `NetworkStackName`: **`WordPress-Network`**
        *   `SecurityStackName`: **`WordPress-Security`**
        *   `DBPassword`: Una contraseña segura para tu base de datos.
        *   `KeyPairName`: El nombre de tu par de claves EC2.
    *   Lanza la pila y espera pacientemente. Esta tardará más (5-10 minutos) mientras se provisiona la RDS.

4.  **Paso 3.4: Verificación**
    *   Cuando la pila `WordPress-Application` esté en estado `CREATE_COMPLETE`, ve a la pestaña **"Salidas" (Outputs)**.
    *   Copia la URL del campo `WebsiteURL` y pégala en tu navegador.
    *   **¡Deberías ver la pantalla de bienvenida para la instalación de WordPress!**

---

### **Fase 4: Documentación (Tiempo Estimado: 20 minutos)**

**Objetivo:** Consolidar tu aprendizaje y generar la evidencia de tu trabajo. Un proyecto sin documentación está incompleto.

**Tu Tarea:** Realiza capturas de pantalla de los siguientes hitos clave del proceso.

1.  **Captura 1: Salidas de la Pila de Red.**
    *   Ve a la pila `WordPress-Network`, pestaña "Salidas".
    *   **Propósito:** Esta captura demuestra que la red se creó correctamente y exportó los valores necesarios (VPCID, SubnetIDs) para las otras pilas.

2.  **Captura 2: Parámetros de la Pila de Seguridad.**
    *   Ve a la pila `WordPress-Security`, pestaña "Parámetros".
    *   **Propósito:** Esta captura prueba cómo la pila de seguridad consumió la salida de la pila de red, mostrando la dependencia `NetworkStackName: WordPress-Network`.

3.  **Captura 3: Salidas de la Pila de Aplicación.**
    *   Ve a la pila `WordPress-Application`, pestaña "Salidas".
    *   **Propósito:** Muestra el resultado final y el punto de acceso (la URL) a la solución completa.

4.  **Captura 4: Pantalla de Instalación de WordPress.**
    *   La página web que se abre desde la URL de la salida anterior.
    *   **Propósito:** Es la prueba final de que todos los componentes (red, seguridad, EC2, RDS, script de instalación) funcionaron en conjunto exitosamente.

---

### **Conclusión del Proyecto**



**Próximos Pasos para Seguir Aprendiendo:**
*   
*   
*   
