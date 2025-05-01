**Política de Nomenclatura y Etiquetado de Recursos AWS para Entornos Formativos y Proyectos (Revisión 6)**

**1. Propósito**

Definir estándares claros y consistentes para nombrar y etiquetar recursos AWS creados en entornos formativos (cursos, laboratorios) o proyectos dentro de la cuenta [Nombre o ID de la Cuenta/OU]. El objetivo es identificar de forma compacta el **contexto** (curso/ciclo/sección o proyecto), la actividad (laboratorio), el creador (alumno/miembro), facilitar la revisión, la limpieza, el seguimiento de costos y fomentar buenas prácticas de gestión en AWS.

**2. Alcance**

Aplica a todos los usuarios (alumnos, miembros de proyecto) y recursos creados para actividades formativas o de desarrollo dentro de los contextos definidos (ej. curso DSN, proyecto CRM-Piloto) en la cuenta AWS especificada.

**3. Convención de Nomenclatura**

**3.1. Formato General:**

`[contexto]-lab<num>-<id_usuario>-<tipo_recurso>-<proposito>`

*   **Separador:** Guiones (`-`).
*   **Caracteres:** Letras minúsculas (`a-z`), números (`0-9`), guiones (`-`).
*   **Longitud:** Concisa, respetando límites de AWS.

**3.2. Componentes del Nombre:**

*   **`[contexto]` (Obligatorio):**
    *   Identificador compacto que define el marco del recurso. **Debe ser definido por el instructor o líder de proyecto**.
    *   *Para Cursos:* Formato `[acronimo_curso][ciclo_num][seccion_letra]` (minúsculas).
        *   *Ejemplo:* `dsn5a` (Curso DSN, Ciclo 5, Sección A).
    *   *Para Proyectos:* Un código corto definido para el proyecto (puede incluir entorno si aplica).
        *   *Ejemplo:* `crmpiloto`, `webappdev`, `datatest`.
    *   *Gobernanza:* El responsable (instructor/líder) debe comunicar el prefijo `[contexto]` exacto a usar.

*   **`lab<num>` (Obligatorio para formación, opcional/adaptable para proyectos):**
    *   Número secuencial del laboratorio o sprint/fase del proyecto (dos dígitos).
    *   *Ejemplos:* `lab01`, `lab12`, `sprint03`, `phase1`.

*   **`<id_usuario>` (Obligatorio):**
    *   Identificador único del usuario: formato `inicialNombreApellido` (minúsculas) u otro identificador corto asignado.
    *   *Ejemplos:* `asmith`, `jdoe`, `usr123`.
    *   *Gobernanza:* Asegurar unicidad y gestionar colisiones (ej. `asmith2`).

*   **`<tipo_recurso>` (Obligatorio):**
    *   Abreviatura estándar del servicio AWS (ver lista de referencia abajo).
    *   *Ejemplos:* `ec2`, `sg`, `s3`, `vpc`, `rds`, `lambda`.

*   **`<proposito>` (Obligatorio):**
    *   Descripción muy breve de la función del recurso.
    *   *Ejemplos:* `web`, `db`, `app`, `logs`, `public`, `private`, `main`, `worker`, `data`, `api`.

**3.3. Lista de Abreviaturas Comunes de Servicios AWS (Referencia):**

| Servicio AWS                 | Abreviatura Sugerida | Descripción Breve                     |
| :--------------------------- | :------------------- | :------------------------------------ |
| EC2 Instance                 | `ec2`                | Servidor virtual                      |
| VPC                          | `vpc`                | Red privada virtual                   |
| Subnet                       | `subnet`             | Subred dentro de una VPC              |
| Security Group               | `sg`                 | Firewall a nivel de instancia         |
| IAM Role                     | `iamrole`            | Rol de permisos para servicios/usuarios |
| IAM Policy                   | `iampolicy`          | Documento de permisos                 |
| S3 Bucket                    | `s3`                 | Almacenamiento de objetos             |
| RDS Instance                 | `rds`                | Base de datos relacional gestionada   |
| Lambda Function              | `lambda`             | Ejecución de código sin servidor      |
| API Gateway                  | `apigw`              | Creación y gestión de APIs            |
| DynamoDB Table               | `ddb`                | Base de datos NoSQL gestionada        |
| CloudWatch Alarm             | `cwalarm`            | Alarma basada en métricas             |
| CloudFormation Stack         | `cfn`                | Infraestructura como Código (AWS)     |
| Route Table                  | `rtb`                | Tabla de rutas de la VPC              |
| Internet Gateway             | `igw`                | Conexión a Internet para VPC          |
| NAT Gateway                  | `natgw`              | Salida a Internet para subnets privadas |
| Elastic Load Balancer (ALB)  | `alb`                | Balanceador de carga de aplicación    |
| SQS Queue                    | `sqs`                | Servicio de colas de mensajes         |
| SNS Topic                    | `sns`                | Servicio de notificaciones            |
| Elastic File System (EFS)    | `efs`                | Almacenamiento de archivos compartido |
| Elastic Block Store (EBS)    | `ebs`                | Volumen de disco para EC2             |
| * (Añadir más según necesidad) * |                      |                                       |

**3.4. Ejemplos Específicos:**

*   **EC2 (Curso DSN, Ciclo 5A, Lab 5, alumno 'asmith', web server):** `dsn5a-lab05-asmith-ec2-web`
*   **VPC (Curso DSN, Ciclo 5A, Lab 3, alumno 'jdoe', principal):** `dsn5a-lab03-jdoe-vpc-main`
*   **Subnet (Curso DSN, Ciclo 5A, Lab 3, alumno 'asmith', privada):** `dsn5a-lab03-asmith-subnet-private`
*   **S3 Bucket (Proyecto CRM Piloto, Lab 2, usuario 'jvega', datos iniciales):** `crmpiloto-lab02-jvega-s3-initialdata`
*   **Lambda (Curso DSN, Ciclo 6B, Lab 10, alumno 'lruz', procesar imágenes):** `dsn6b-lab10-lruz-lambda-imgproc`
*   **RDS DB (Proyecto WebApp Dev, Sprint 4, usuario 'mrios', base de datos usuarios):** `webappdev-sprint04-mrios-rds-userdb`
*   **Security Group (Curso DSN, Ciclo 5A, Lab 5, alumno 'asmith', acceso público a web):** `dsn5a-lab05-asmith-sg-webpublic`

**4. Política de Etiquetado (Tagging)**

El etiquetado es **FUNDAMENTAL** para gestión, filtrado y costos. Todos los recursos que lo soporten DEBEN tener:

**4.1. Etiquetas Obligatorias:**

*   **`Name`**: (Valor: El nombre completo del recurso según la convención).
    *   *Ejemplo:* `dsn5a-lab05-asmith-ec2-web`
*   **`ContextID`**: (Valor: El prefijo `[contexto]` usado en el nombre). *Identifica el marco (curso/proyecto)*.
    *   *Ejemplo:* `dsn5a`, `crmpiloto`
*   **`ActivityID`**: (Valor: El identificador `lab<num>` o `sprint<num>` usado en el nombre). *Identifica la tarea específica*.
    *   *Ejemplo:* `lab05`, `sprint04`
*   **`UserID`**: (Valor: El identificador de usuario `<id_usuario>` usado en el nombre). *Identifica al creador*.
    *   *Ejemplo:* `asmith`, `jvega`
*   **`CreationDate`**: (Valor: Fecha de creación `YYYY-MM-DD`).
*   **`CleanupDate`**: (Valor: Fecha límite para borrar el recurso `YYYY-MM-DD`). *Esencial para limpieza*.
*   **`CycleSection` (Obligatorio para Cursos, Opcional/N/A para Proyectos):** (Valor: Ciclo y Sección, ej. `5A`). *Permite agrupar por clase*.

**4.2. Etiquetas Recomendadas:**

*   `ProjectName`: (Valor: Nombre completo del proyecto/curso, ej. `Desarrollo Soluciones Nube`, `Piloto CRM`).
*   `OwnerEmail`: (Valor: Email del responsable o lista de equipo).

**4.3. Formato de Etiquetas:**

*   **Claves:** `PascalCase` (ej. `ContextID`, `ActivityID`).
*   **Valores:** Minúsculas o formato especificado (fechas, `labXX`, `5A`).

**5. Gobernanza y Cumplimiento**

*   **Instrucción Clara:** El responsable (instructor/líder) comunica la política, el **prefijo `[contexto]` exacto**, el formato de `ActivityID` y `UserID`.
*   **Monitorización:** Usar filtros por etiquetas (`ContextID`, `ActivityID`, `UserID`, `CycleSection`) para revisión y gestión de costos.
*   **Limpieza:** Usar `CleanupDate` para identificar recursos a eliminar (automatizar si es posible).
*   **Consecuencias:** Definir y comunicar las del incumplimiento.

**6. Ejemplo de Lanzamiento de EC2 (Recapitulando)**

*   **Escenario:** Curso DSN, alumno `asmith`, ciclo 5, sección A. Prefijo: `dsn5a`. Laboratorio 5 (`lab05`). Crear servidor web (`web`). Fecha creación `2023-10-27`. Fecha limpieza `2023-11-03`.

*   **Nombre (Tag `Name`):** `dsn5a-lab05-asmith-ec2-web`

*   **Otras Etiquetas Obligatorias:**
    *   `ContextID`: `dsn5a`
    *   `ActivityID`: `lab05`
    *   `UserID`: `asmith`
    *   `CycleSection`: `5A`
    *   `CreationDate`: `2023-10-27`
    *   `CleanupDate`: `2023-11-03`

