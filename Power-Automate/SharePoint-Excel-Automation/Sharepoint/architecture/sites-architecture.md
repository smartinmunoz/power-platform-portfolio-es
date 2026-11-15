# Arquitectura de sitios de SharePoint

## 1. Descripción general

Este proyecto presenta una arquitectura de SharePoint Online diseñada para una solución de recepción, validación, procesamiento y generación de informes documentales integrada con Power Automate.

La arquitectura consta de:

- Un **sitio principal de SharePoint** para recibir documentos y proporcionar acceso controlado a sus copias.
- Un **subsitio de gestión** para la validación, el procesamiento, la configuración y la supervisión.

Todos los nombres y configuraciones son ficticios y se utilizan con fines de portfolio.

> **Aviso:** Este documento describe una arquitectura conceptual de demostración. No incluye direcciones URL de producción, credenciales, identificadores reales de grupos ni datos empresariales confidenciales.

### 1.1 Estructura de sitios

```text
Sitio principal de SharePoint
|
|-- Lista de recepción de solicitudes
|-- Biblioteca de documentos derivados
|
`-- Subsitio de gestión
    |
    |-- Biblioteca de validación
    |-- Biblioteca de archivos temporales
    |-- Biblioteca de exportaciones
    |-- Lista de registros procesados
    |-- Lista de configuración de centros
    `-- Lista de registros de ejecución
```

La arquitectura incluye cuatro listas y cuatro bibliotecas de documentos de SharePoint distribuidas entre el sitio principal y el subsitio de gestión.

## 2. Sitio principal de SharePoint

### 2.1 Finalidad

El sitio principal de SharePoint ofrece un punto de entrada controlado para el personal externo y los usuarios internos de gestión.

Permite presentar solicitudes y consultar copias de documentos en modo de solo lectura.

### 2.2 Lista de recepción de solicitudes

El personal externo presenta solicitudes mediante un formulario personalizado de SharePoint.

El formulario admite:

- Información de la solicitud.
- Archivos Excel adjuntos.
- Observaciones opcionales.
- Procesamiento posterior mediante Power Automate.

La lista utiliza personalizaciones JSON para el diseño del formulario y la barra de comandos.

FL01 recupera los adjuntos, distribuye copias de los documentos y aplica permisos a las solicitudes.

Una vez que FL01 aplica los permisos a nivel de elemento, la persona que creó la solicitud conserva acceso de **lectura** a su propia solicitud.

El resto del personal externo no debe acceder a solicitudes que no haya creado.

### 2.3 Biblioteca de documentos derivados

Almacena las copias documentales creadas por FL01 durante la recepción de solicitudes.

Los usuarios autorizados del sitio principal, incluido el personal externo cuando corresponda, disponen de acceso de solo lectura.

Las operaciones de creación y administración de documentos se realizan mediante una conexión de automatización con los permisos adecuados.

La biblioteca sigue un modelo de seguridad distinto del de la biblioteca de validación, cuyo acceso está restringido.

## 3. Subsitio de gestión

### 3.1 Finalidad

El subsitio de gestión centraliza la validación documental, los resultados del procesamiento, la configuración y la supervisión.

Los grupos de centros tienen acceso general de **lectura** al subsitio, sujeto a las restricciones de permisos de cada recurso y elemento.

El personal externo no tiene acceso general al subsitio de gestión.

### 3.2 Biblioteca de validación

Almacena los archivos Excel pendientes de validación y posterior procesamiento.

La biblioteca utiliza permisos únicos para impedir el acceso de los grupos de centros y del personal externo.

Durante la validación, los grupos Site Owners y Site Members tienen permisos de **edición**.

El ciclo de vida documental utiliza los siguientes estados:

| Estado | Descripción |
|---|---|
| `Pending Validation` | Documento pendiente de revisión |
| `Validated` | Documento aprobado para su procesamiento |
| `Processing` | FL02 está en ejecución y el documento de origen está protegido |
| `Processed` | El procesamiento y el registro de ejecución requerido han finalizado correctamente |
| `Error` | El procesamiento ha fallado o no ha podido completarse correctamente |

Cuando un documento cambia al estado `Validated`, FL02 inicia el procesamiento y aplica protección a nivel de archivo.

El flujo realiza estas operaciones:

1. Interrumpe la herencia de permisos del documento de origen.
2. Concede acceso de **lectura** al grupo Site Owners.
3. Concede acceso de **lectura** al grupo Site Members.
4. Cambia el estado del documento de `Validated` a `Processing`.
5. Procesa los datos Excel, crea registros en SharePoint, genera el libro de exportación e invoca FL04 para registrar la ejecución.
6. Cambia el estado a `Processed` si el procesamiento y el registro finalizan correctamente, o a `Error` si no pueden completarse correctamente.

El estado `Processing` indica que el documento ha entrado en la fase protegida de procesamiento; no garantiza que esta termine correctamente.

El estado `Processed` confirma la finalización correcta, mientras que `Error` identifica errores de procesamiento o de registro.

Los administradores de la colección de sitios conservan el control administrativo.

Las conexiones de automatización autorizadas deben mantener los permisos necesarios para completar el procesamiento y actualizar los metadatos del documento.

Es necesario comprobar los permisos efectivos para garantizar que los usuarios ordinarios de gestión no puedan modificar documentos de origen protegidos mediante otras pertenencias a grupos o asignaciones directas.

Consulta [Biblioteca de validación](../libraries/validation-library.md) para obtener más detalles.

### 3.3 Biblioteca de archivos temporales

Almacena los libros Excel temporales creados durante el procesamiento de FL02.

Se encuentra en el subsitio de gestión y su acceso está restringido a las cuentas autorizadas de gestión y automatización.

FL03 intenta eliminar periódicamente los archivos temporales con más de ocho horas de antigüedad.

El flujo no comprueba explícitamente si un archivo sigue siendo necesario para una operación de procesamiento activa.

Los errores de eliminación, incluidos los causados por bloqueos activos de archivos Excel, se tratan mediante diagnósticos sin interrumpir las demás operaciones de limpieza.

Antes de desplegar la solución en producción, debe revisarse el umbral de retención de ocho horas en función de la duración prevista de los procesos.

Consulta [Biblioteca de archivos temporales](../libraries/temporary-files-library.md) para obtener más detalles.

### 3.4 Biblioteca de exportaciones

Almacena los libros Excel de exportación estructurados que genera FL02.

El proceso de exportación utiliza una plantilla Excel y Office Scripts para completar la estructura de salida requerida.

La biblioteca proporciona acceso de solo lectura a:

- Site Owners.
- Site Members.
- Grupos de centros autorizados.

El personal externo no tiene acceso.

FL02 crea y actualiza los libros de exportación mediante una conexión de automatización debidamente autorizada.

La biblioteca debe mantener sus restricciones de acceso independientemente de los permisos aplicados a los elementos individuales de la lista de registros procesados.

Consulta [Biblioteca de exportaciones](../libraries/export-library.md) para obtener más detalles.

### 3.5 Lista de registros procesados

Almacena los registros principales y asociados extraídos de los archivos Excel validados.

FL02 crea los registros de SharePoint y mantiene las relaciones entre registros principales y asociados.

FL05 aplica permisos a nivel de elemento según el centro asociado a cada registro.

El modelo de permisos previsto es:

- Grupo del centro asignado: **lectura**.
- Otros grupos de centros: **sin acceso**.
- Site Owners: **edición**.
- Site Members: **edición**.
- Personal externo: **sin acceso**.

Si falta la configuración del centro, FL05 aplica un modelo de permisos restrictivo y registra un error de diagnóstico.

Deben comprobarse los permisos efectivos para garantizar que los usuarios no puedan acceder a registros asignados a otros centros.

Consulta [Lista de registros procesados](../lists/processed-records.md) para obtener más detalles.

### 3.6 Lista de configuración de centros

Una única lista de configuración mantiene la relación entre los centros operativos y los grupos de SharePoint.

La lista contiene:

- Identificador o nombre del centro.
- ID del grupo de SharePoint asociado al centro.
- Enlace de administración del grupo.

Los usuarios autorizados con acceso al subsitio de gestión pueden leer la lista.

La modificación de la configuración de centros requiere permisos autorizados específicos.

La administración de la pertenencia a los grupos permanece restringida a los usuarios autorizados para gestionar los correspondientes grupos de SharePoint.

FL05 utiliza esta lista para determinar qué grupo debe recibir acceso a cada registro procesado.

Las asignaciones de centros inexistentes, duplicadas o no válidas no deben provocar accesos no deseados.

Consulta [Lista de configuración de centros](../lists/centers-configuration.md) para obtener más detalles.

### 3.7 Lista de registros de ejecución

Almacena la información de ejecución y procesamiento registrada mediante FL04.

La lista proporciona acceso de **lectura** a:

- Site Owners.
- Site Members.

Los grupos de centros y el personal externo no tienen acceso.

La lista utiliza permisos restringidos, en lugar de heredar sin restricciones los permisos del subsitio de gestión.

FL04 crea los registros mediante una conexión de automatización debidamente autorizada.

La estructura de registro permite almacenar:

- Identificadores de referencia del procesamiento.
- Identificadores del documento de origen.
- Identificadores de ejecución.
- Número de registros leídos.
- Número de registros creados.
- Número de operaciones fallidas de creación de registros en SharePoint.
- Estado del procesamiento.
- Detalles de errores.

FL04 contempla cuatro estados conceptuales de ejecución:

- `Success`.
- `Partial Success`.
- `Error`.
- `No Records`.

Estos estados de ejecución son distintos de los estados del ciclo de vida documental gestionados por FL02.

Consulta [Lista de registros de ejecución](../lists/processing-logs.md) para obtener más detalles.

## 4. Matriz de permisos

La siguiente matriz resume el modelo conceptual de acceso al sitio principal y al subsitio de gestión de SharePoint.

| Recurso | Site Owners | Site Members | Grupos de centros | Personal externo |
|---|---|---|---|---|
| Sitio principal de SharePoint | Según el rol | Según el rol | Según la asignación | Acceso autorizado |
| Lista de recepción de solicitudes | Lectura de solicitudes presentadas | Lectura de solicitudes presentadas | Sin acceso | Crear solicitudes; leer las propias |
| Biblioteca de documentos derivados | Lectura | Lectura | Según la asignación | Lectura |
| Subsitio de gestión | Según el rol | Según el rol | Lectura | Sin acceso |
| Biblioteca de validación: durante la validación | Edición | Edición | Sin acceso | Sin acceso |
| Biblioteca de validación: después de la protección de FL02 | Lectura | Lectura | Sin acceso | Sin acceso |
| Biblioteca de archivos temporales | Acceso administrativo | Solo acceso autorizado | Sin acceso | Sin acceso |
| Biblioteca de exportaciones | Lectura | Lectura | Lectura | Sin acceso |
| Lista de registros procesados | Edición | Edición | Solo registros asignados | Sin acceso |
| Lista de configuración de centros | Lectura | Lectura | Lectura | Sin acceso |
| Lista de registros de ejecución | Lectura | Lectura | Sin acceso | Sin acceso |

### 4.1 Acceso de la persona que crea la solicitud

La lista de recepción de solicitudes utiliza permisos a nivel de elemento.

El personal externo puede crear solicitudes, pero, una vez que FL01 aplica los permisos correspondientes, cada persona conserva acceso de **lectura** únicamente a sus propias solicitudes.

El resto del personal externo no debe acceder a solicitudes ajenas.

Los permisos iniciales de la lista deben configurarse cuidadosamente, porque FL01 aplica la protección a nivel de elemento después de crear la solicitud.

### 4.2 Acceso a documentos

La biblioteca de documentos derivados proporciona acceso de **lectura** a los usuarios autorizados del sitio principal, incluido el personal externo.

La biblioteca de validación está restringida a usuarios de gestión.

Durante la validación, Site Owners y Site Members pueden editar los documentos.

Cuando FL02 comienza a procesar un documento validado, interrumpe la herencia de permisos a nivel de archivo y concede acceso de **lectura** a Site Owners y Site Members.

La biblioteca de archivos temporales está restringida a las cuentas autorizadas de gestión y automatización.

La biblioteca de exportaciones proporciona acceso de **lectura** a Site Owners, Site Members y grupos de centros autorizados.

### 4.3 Acceso administrativo

Los administradores de la colección de sitios conservan sus privilegios administrativos sobre los recursos de SharePoint correspondientes.

Los permisos administrativos y las conexiones de automatización debidamente autorizadas se gestionan por separado del acceso de los usuarios ordinarios.

### 4.4 Consideraciones importantes

La matriz describe el modelo de permisos previsto, no una configuración de producción verificada.

El acceso efectivo depende de:

- Pertenencia a grupos de SharePoint.
- Herencia de permisos.
- Asignaciones directas de permisos.
- Pertenencia a varios grupos.
- Privilegios de las conexiones de automatización.
- Privilegios de administración de la colección de sitios.

Todos los permisos deben validarse en un entorno controlado de SharePoint.

## 5. Estrategia de herencia de permisos

La herencia de permisos de SharePoint se interrumpe selectivamente para aplicar seguridad específica por recurso y por elemento.

La arquitectura combina permisos a nivel de sitio, restricciones a nivel de biblioteca y asignaciones automatizadas de permisos a nivel de elemento.

### 5.1 Lista de recepción de solicitudes

- El personal externo autorizado puede crear nuevas solicitudes.
- FL01 interrumpe la herencia de permisos de cada solicitud presentada.
- La persona que crea la solicitud recibe acceso de **lectura** a su propia solicitud.
- Site Owners y Site Members reciben acceso de **lectura**.
- Se excluye al resto del personal externo y a los grupos de centros.
- Los adjuntos quedan protegidos mediante los permisos efectivos del elemento correspondiente.
- Los permisos iniciales de la lista deben impedir accesos no deseados antes de que FL01 complete sus asignaciones.

### 5.2 Biblioteca de documentos derivados

- Los usuarios autorizados del sitio principal reciben acceso de **lectura**.
- El personal externo, Site Owners y Site Members pueden consultar las copias de los documentos.
- Las operaciones de creación y administración se realizan mediante una conexión de automatización debidamente autorizada.

### 5.3 Biblioteca de validación

- Se interrumpe la herencia de permisos a nivel de biblioteca.
- Se excluye a los grupos de centros y al personal externo.
- Site Owners y Site Members tienen acceso de **edición** durante la validación.
- Cuando FL02 procesa un documento validado, interrumpe la herencia de permisos a nivel de archivo.
- FL02 asigna permisos de **lectura** a Site Owners y Site Members.
- El estado del documento cambia a `Processing`.
- Los administradores de la colección de sitios conservan el control administrativo.

### 5.4 Biblioteca de archivos temporales

- El acceso está restringido a las cuentas autorizadas de gestión y automatización.
- Se excluye a los grupos de centros y al personal externo.
- FL02 crea archivos Excel temporales para el procesamiento.
- FL03 intenta eliminar los archivos temporales con más de ocho horas de antigüedad.
- FL03 trata los errores de eliminación mediante diagnósticos.
- El flujo no comprueba explícitamente si un archivo es necesario para una operación de procesamiento activa.

### 5.5 Biblioteca de exportaciones

- Site Owners, Site Members y los grupos de centros reciben acceso de **lectura**.
- Se excluye al personal externo.
- FL02 crea documentos de exportación mediante una conexión de automatización debidamente autorizada.

### 5.6 Lista de registros procesados

- FL02 crea registros principales y asociados.
- FL05 aplica permisos a nivel de elemento según el centro asignado al registro.
- Los grupos de centros asignados reciben acceso de **lectura**.
- Site Owners y Site Members reciben acceso de **edición**.
- Los demás grupos de centros no reciben acceso.
- Si falta la configuración de un centro, se aplica un modelo de permisos restrictivo.
- Los permisos efectivos deben probarse en SharePoint.

### 5.7 Lista de configuración de centros

- Site Owners, Site Members y los grupos de centros reciben acceso de **lectura**.
- Se excluye al personal externo.
- La lista almacena las correspondencias entre centros y grupos de SharePoint utilizadas por FL05.
- Los cambios en los elementos de configuración requieren permisos específicos.

### 5.8 Lista de registros de ejecución

- Se interrumpe la herencia de permisos a nivel de lista.
- Site Owners y Site Members reciben acceso de **lectura**.
- Se excluye a los grupos de centros y al personal externo.
- FL04 crea registros mediante una conexión de SharePoint debidamente autorizada.

### 5.9 Validación de permisos

Todas las interrupciones de herencia y asignaciones de permisos deben probarse en un entorno controlado de SharePoint.

Es necesario verificar el acceso efectivo de los usuarios que pertenecen a varios grupos de SharePoint y de las conexiones de automatización que realizan operaciones privilegiadas.

## 6. Integración con Power Automate

La arquitectura integra cinco flujos conceptuales de Power Automate.

| Flujo | Responsabilidad |
|---|---|
| FL01 | Recepción de solicitudes, distribución documental y permisos a nivel de solicitud |
| FL02 | Procesamiento de Excel validados, protección documental, creación de registros y generación de exportaciones |
| FL03 | Limpieza programada de archivos temporales |
| FL04 | Registro de resultados y seguimiento de ejecuciones |
| FL05 | Permisos a nivel de elemento basados en centros |

### 6.1 Coordinación entre flujos

1. FL01 recibe una solicitud de SharePoint y distribuye copias de los documentos Excel.
2. Los usuarios autorizados de gestión validan el documento.
3. FL02 procesa el libro Excel validado y crea registros en SharePoint.
4. FL05 aplica permisos basados en el centro a los registros recién creados.
5. FL02 genera un libro de exportación e invoca FL04 para registrar la ejecución.
6. FL02 actualiza el estado del documento de origen según el resultado del procesamiento.
7. FL03 realiza de forma independiente la limpieza programada de los archivos temporales.

Los flujos se documentan como demostraciones conceptuales, no como paquetes de Power Automate directamente importables.

## 7. Consideraciones de seguridad

- Aplicar el principio de mínimo privilegio.
- Separar la validación documental del acceso general de los centros.
- Proteger los documentos de origen procesados frente a modificaciones no autorizadas.
- Restringir los registros de ejecución a los usuarios de gestión.
- Restringir los archivos temporales a las cuentas autorizadas.
- Utilizar grupos de SharePoint específicos para el acceso por centros.
- Impedir accesos no deseados a solicitudes recién creadas antes de que FL01 aplique los permisos a nivel de elemento.
- Validar la correspondencia entre centros y grupos antes de asignar permisos a registros.
- Evitar almacenar identificadores reales de grupos, URL de producción o información confidencial en repositorios públicos.
- Revisar los permisos efectivos cuando los usuarios pertenezcan a varios grupos.
- Validar las interrupciones de herencia y las asignaciones de permisos en un entorno de pruebas controlado.
- Garantizar que las conexiones de automatización mantengan los permisos necesarios para completar operaciones privilegiadas.

## 8. Estado de la documentación

**Estado:** arquitectura conceptual de SharePoint documentada.

El portfolio incluye documentación sobre:

- Sitio principal y subsitio de gestión de SharePoint.
- Jerarquía de sitios y distribución de recursos.
- Cuatro listas de SharePoint.
- Cuatro bibliotecas de documentos de SharePoint.
- Configuración de formulario personalizado mediante JSON.
- Formato JSON de barras de comandos.
- Estrategias de permisos a nivel de sitio, biblioteca y elemento.
- Integración con los flujos FL01 a FL05 de Power Automate.
- Ciclo de vida documental y estados de procesamiento.
- Limpieza programada de archivos temporales.
- Registro centralizado de ejecuciones.
- Permisos de registros basados en centros.

### 8.1 Validación de la implementación

Los siguientes aspectos requieren validación en un entorno de SharePoint de destino:

- Configuración del sitio y del subsitio.
- Pertenencia a grupos de SharePoint y permisos efectivos.
- Herencia de permisos y asignaciones automatizadas.
- Seguridad inicial de las solicitudes antes de que FL01 termine.
- Funcionamiento del formulario personalizado y de la barra de comandos.
- Conexiones de Power Automate y permisos de ejecución.
- Integración de Office Scripts con los archivos Excel de ejemplo.
- Pruebas integrales de procesamiento documental y seguridad.
- Comportamiento de la retención de archivos temporales.
- Gestión de errores y escenarios de recuperación.

Este documento describe una arquitectura conceptual y no es una plantilla de aprovisionamiento automático de SharePoint.
