# Subsitio de gestión de SharePoint

## 1. Descripción general

El subsitio de gestión es un subsitio de SharePoint Online integrado en el sitio principal de SharePoint.

Proporciona un entorno centralizado para la validación documental, el procesamiento de Excel, la gestión de registros, la configuración de centros y la supervisión de ejecuciones.

La solución integra SharePoint Online con los flujos de Power Automate FL01–FL05.

> **Aviso sobre el portfolio:** Esta documentación describe una arquitectura de demostración ficticia. No incluye direcciones URL de producción, identificadores reales de grupos, credenciales ni información empresarial confidencial.

## 2. Estructura del subsitio

```text
Subsitio de gestión
|
|-- Biblioteca de validación
|-- Biblioteca de archivos temporales
|-- Biblioteca de exportaciones
|
|-- Lista de registros procesados
|-- Lista de configuración de centros
`-- Lista de registros de ejecución
```

## 3. Roles de usuario

El subsitio utiliza grupos de SharePoint y permisos específicos para cada recurso.

| Rol | Descripción |
|---|---|
| Site Collection Administrators | Conservan el control administrativo sobre la colección de sitios |
| Site Owners | Usuarios de gestión con permisos específicos según el recurso |
| Site Members | Usuarios operativos internos |
| Center Groups | Usuarios asociados a cada centro operativo |
| External Staff | No tienen acceso general al subsitio de gestión |

Los grupos de centros tienen acceso de lectura al subsitio, excepto cuando se interrumpe la herencia o se restringe el acceso mediante permisos a nivel de elemento.

Los permisos efectivos de Site Owners y Site Members también pueden variar según el recurso.

## 4. Biblioteca de validación

### 4.1 Finalidad

La biblioteca de validación almacena los documentos Excel pendientes de validación y procesamiento.

Los documentos se revisan y validan antes de continuar con el procesamiento automatizado.

### 4.2 Configuración de permisos

La biblioteca utiliza permisos únicos en lugar de heredar sin restricciones los del subsitio.

- **Site Owners:** acceso de gestión autorizado.
- **Site Members:** acceso de gestión autorizado.
- **Center Groups:** sin acceso.
- **External Staff:** sin acceso.
- **Site Collection Administrators:** control administrativo.

### 4.3 Protección de documentos procesados

Cuando se valida un documento, FL02 inicia su procesamiento y modifica los permisos a nivel de archivo para proteger el libro de origen.

El flujo:

1. Interrumpe la herencia de permisos del documento.
2. Asigna permisos de lectura al grupo Site Owners.
3. Asigna permisos de lectura al grupo Site Members.
4. Cambia el estado del documento de `Validated` a `Processing`.
5. Continúa con el procesamiento de Excel.
6. Actualiza el estado a `Processed` cuando el procesamiento y el registro de ejecución requerido finalizan correctamente, o a `Error` si no pueden completarse satisfactoriamente.

Tras modificar los permisos, los usuarios ordinarios de Site Owners y Site Members solo tienen acceso de lectura al documento protegido.

Los administradores de la colección de sitios conservan el control administrativo conforme al modelo de permisos documentado.

**Importante:** El estado `Processing` indica que el documento está siendo procesado, pero no garantiza que el proceso termine correctamente. El estado final distingue entre el procesamiento satisfactorio (`Processed`) y los errores de procesamiento o registro (`Error`).

## 5. Biblioteca de archivos temporales

### 5.1 Finalidad

La biblioteca de archivos temporales proporciona un espacio de trabajo específico para los archivos Excel generados durante el procesamiento automatizado.

FL02 crea copias temporales de trabajo de los documentos validados, manteniendo separados los archivos originales y las operaciones intermedias.

FL03 elimina periódicamente los archivos temporales con más de ocho horas de antigüedad.

### 5.2 Permisos

| Rol | Acceso |
|---|---|
| Site Owners | Control total |
| Miembros de gestión autorizados | El necesario para la administración |
| Conexión de procesamiento de Power Automate | Lectura / escritura / eliminación |
| Center Groups | Sin acceso |
| External Staff | Sin acceso |

La biblioteca utiliza permisos restringidos para impedir el acceso no autorizado a los archivos temporales de procesamiento.

El umbral de ocho horas determina qué archivos pueden eliminarse, pero no establece una hora exacta de eliminación.

Para más detalles de implementación, consulta [Biblioteca de archivos temporales](../libraries/temporary-files-library.md).

## 6. Biblioteca de exportaciones

### 6.1 Finalidad

La biblioteca de exportaciones almacena los libros Excel generados durante el procesamiento.

Estos archivos proporcionan resultados estructurados para los usuarios autorizados.

### 6.2 Permisos

| Rol | Permiso |
|---|---|
| Site Collection Administrators | Control administrativo |
| Site Owners | Lectura |
| Site Members | Lectura |
| Center Groups | Lectura |
| External Staff | Sin acceso |

La biblioteca puede utilizar personalizaciones JSON de la barra de comandos de SharePoint.

El formato JSON se emplea para mejorar la presentación y la usabilidad, no para sustituir el control de acceso.

## 7. Lista de registros procesados

### 7.1 Finalidad

La lista de registros procesados almacena la información extraída de los libros Excel validados.

El flujo de procesamiento crea registros principales y registros asociados.

Los registros asociados hacen referencia a su correspondiente registro principal.

### 7.2 Acceso basado en centros

El acceso se controla mediante permisos a nivel de elemento.

FL05 asigna permisos según el centro asociado a cada registro.

Esto permite que los grupos de centros accedan únicamente a los registros que tienen asignados.

El acceso general de lectura al subsitio no concede automáticamente acceso a todos los registros de esta lista.

### 7.3 Relación con FL02 y FL05

FL02 crea los registros procesados.

FL05 aplica los permisos necesarios de SharePoint a cada elemento de la lista.

La correspondencia entre centros y grupos se obtiene de la lista de configuración de centros.

## 8. Lista de configuración de centros

### 8.1 Finalidad

Una única lista de SharePoint mantiene la relación entre los centros operativos y sus correspondientes grupos de SharePoint.

### 8.2 Campos de configuración

| Campo | Descripción |
|---|---|
| Center | Nombre o identificador del centro |
| SharePoint Group ID | Identificador del grupo asociado al centro |
| Group Management Link | Enlace que utilizan los administradores autorizados para gestionar la pertenencia al grupo |

Todos los usuarios que tienen acceso al subsitio de gestión pueden leer la lista.

### 8.3 Administración de grupos

Los enlaces de administración de grupos están disponibles como referencia.

Sin embargo, disponer de un enlace no concede permisos para modificar la pertenencia al grupo.

Solo los usuarios con los privilegios adecuados de administración de grupos de SharePoint pueden gestionar dichos grupos.

### 8.4 Integración con FL05

FL05 utiliza la configuración de centros para identificar el grupo de SharePoint correspondiente y asignar permisos a nivel de elemento a los registros procesados.

El portfolio público no incluye identificadores reales de grupos de SharePoint ni direcciones URL administrativas.

## 9. Lista de registros de ejecución

### 9.1 Finalidad

La lista de registros de ejecución permite mantener la trazabilidad del procesamiento automatizado de documentos.

FL04 crea entradas con información sobre las ejecuciones y sus resultados.

### 9.2 Información de ejemplo

Según la configuración del flujo, una entrada puede contener:

- Identificador de ejecución del procesamiento.
- Referencia al documento de origen.
- Estado del procesamiento.
- Número de registros creados correctamente.
- Información sobre errores.
- Resumen del procesamiento.

El esquema exacto de columnas se documenta por separado.

### 9.3 Permisos

| Rol | Permiso |
|---|---|
| Site Collection Administrators | Control administrativo |
| Site Owners | Lectura |
| Site Members | Lectura |
| Center Groups | Sin acceso |
| External Staff | Sin acceso |

La lista debe utilizar permisos restringidos para impedir que los grupos de centros hereden el acceso general de lectura del subsitio.

FL04 escribe los registros mediante una conexión de Power Automate debidamente autorizada.

El acceso de solo lectura de los usuarios ordinarios no impide que una conexión de automatización autorizada cree registros.

## 10. Matriz de permisos

La siguiente matriz resume el modelo conceptual de permisos del subsitio de gestión.

| Recurso | Site Owners | Site Members | Center Groups | External Staff |
|---|---|---|---|---|
| Subsitio de gestión | Según el rol | Según el rol | Lectura | Sin acceso |
| Biblioteca de validación: durante la validación | Edición | Edición | Sin acceso | Sin acceso |
| Biblioteca de validación: después de la protección de FL02 | Lectura | Lectura | Sin acceso | Sin acceso |
| Biblioteca de exportaciones | Lectura | Lectura | Lectura | Sin acceso |
| Biblioteca de archivos temporales | Control total | Restringido a miembros de gestión autorizados | Sin acceso | Sin acceso |
| Lista de registros procesados | Edición | Edición | Solo registros asignados | Sin acceso |
| Lista de configuración de centros | Lectura | Lectura | Lectura | Sin acceso |
| Lista de registros de ejecución | Lectura | Lectura | Sin acceso | Sin acceso |

### Acceso a la biblioteca de validación

Durante la validación, Site Owners y Site Members pueden editar documentos.

Cuando FL02 procesa un documento validado, interrumpe la herencia de permisos a nivel de archivo y asigna acceso de lectura a Site Owners y Site Members.

Los administradores de la colección de sitios conservan el control administrativo.

### Acceso basado en centros

Los grupos de centros tienen acceso general de lectura al subsitio de gestión.

Sin embargo, el acceso a cada recurso depende de sus permisos específicos.

FL05 asigna permisos a los elementos de la lista de registros procesados según la correspondencia entre centros y grupos almacenada en la lista de configuración de centros.

Los grupos de centros no reciben automáticamente acceso a todos los registros procesados.

### Recursos restringidos

La biblioteca de validación y la lista de registros de ejecución excluyen a los grupos de centros y al personal externo.

El personal externo no tiene acceso al subsitio de gestión.

### Acceso a la biblioteca de archivos temporales

La biblioteca de archivos temporales utiliza acceso restringido para proteger los libros Excel intermedios generados por FL02.

| Rol | Acceso |
|---|---|
| Site Owners | Control total |
| Miembros de gestión autorizados | El necesario para la administración |
| Conexión de procesamiento de Power Automate | Lectura / escritura / eliminación |
| Center Groups | Sin acceso |
| External Staff | Sin acceso |

FL03 utiliza una conexión de automatización autorizada para eliminar archivos temporales con más de ocho horas de antigüedad.

Los errores de eliminación, incluidos los archivos bloqueados por operaciones activas de Excel, deben tratarse sin interrumpir el resto de las operaciones de limpieza.

## 11. Herencia de permisos

El subsitio de gestión combina el acceso general a nivel de subsitio con restricciones específicas de cada recurso y elemento.

Los grupos de centros tienen acceso general de lectura al subsitio, pero esto no concede automáticamente acceso a todas las listas, bibliotecas o documentos.

### Biblioteca de validación

- Se interrumpe la herencia de permisos a nivel de biblioteca.
- Se excluye a Center Groups y External Staff.
- Site Owners y Site Members tienen acceso de edición durante la validación.
- FL02 interrumpe la herencia de permisos a nivel de archivo cuando procesa un documento validado.
- FL02 asigna acceso de lectura a Site Owners y Site Members.
- Los administradores de la colección de sitios conservan el control administrativo.

### Biblioteca de archivos temporales

- Se interrumpe la herencia de permisos de la biblioteca para restringir el acceso a los libros temporales de procesamiento.
- Site Owners conserva el control total.
- Los miembros de gestión autorizados reciben únicamente los permisos necesarios para la administración.
- Las conexiones de Power Automate de procesamiento y limpieza reciben los permisos necesarios para crear, modificar, leer y eliminar archivos temporales.
- Center Groups y External Staff no tienen acceso.
- FL02 crea libros Excel temporales durante el procesamiento.
- FL03 intenta eliminar los archivos temporales con más de ocho horas de antigüedad.
- Los errores de eliminación se tratan mediante diagnósticos sin interrumpir el resto de las operaciones de limpieza.
- Los permisos efectivos deben comprobarse en el entorno de SharePoint de destino.

### Biblioteca de exportaciones

- Site Owners, Site Members y Center Groups reciben acceso de lectura.
- Se excluye a External Staff.
- FL02 crea libros de exportación mediante una conexión de SharePoint debidamente autorizada.

### Lista de registros procesados

- FL02 crea registros principales y asociados.
- FL05 aplica permisos a nivel de elemento según el centro asignado.
- FL05 recupera el SharePoint Group ID correspondiente de la lista de configuración de centros.
- Los grupos de centros reciben acceso únicamente a los registros asignados expresamente a su grupo.
- El acceso general de lectura al subsitio no concede automáticamente acceso a todos los registros procesados.
- Site Owners recibe acceso de edición a los registros procesados.
- Site Members recibe acceso de edición a los registros procesados.

### Lista de configuración de centros

- Site Owners, Site Members y Center Groups reciben acceso de lectura.
- Se excluye a External Staff.
- Los cambios de configuración requieren permisos específicos.
- Los enlaces de administración de grupos no conceden automáticamente privilegios para administrar dichos grupos.

### Lista de registros de ejecución

- Se interrumpe la herencia de permisos a nivel de lista.
- Site Owners y Site Members reciben acceso de lectura.
- Se excluye a Center Groups y External Staff.
- FL04 crea registros mediante una conexión de SharePoint debidamente autorizada.

### Validación de permisos

Todos los cambios de herencia y las asignaciones automatizadas de permisos deben probarse en un entorno controlado de SharePoint.

Es necesario verificar los permisos efectivos de los usuarios que pertenecen a varios grupos de SharePoint y de las conexiones de automatización que realizan operaciones privilegiadas.

## 12. Integración con Power Automate

El subsitio de gestión permite validar documentos, procesar Excel, crear registros, generar exportaciones, registrar ejecuciones y gestionar permisos basados en centros.

La solución utiliza cinco flujos conceptuales de Power Automate.

### 12.1 Responsabilidades de los flujos

| Flujo | Responsabilidad en el subsitio de gestión | Recursos principales |
|---|---|---|
| FL01 | Crea una copia de cada adjunto Excel recibido para su validación interna | Biblioteca de validación |
| FL02 | Procesa archivos Excel validados, protege los documentos de origen, crea registros y genera libros de exportación | Biblioteca de validación, lista de registros procesados, biblioteca de exportaciones |
| FL03 | Elimina archivos temporales de procesamiento que superan el umbral de retención configurado | Archivos temporales de procesamiento |
| FL04 | Crea registros de ejecución y devuelve el identificador `Id_Log` | Lista de registros de ejecución |
| FL05 | Aplica permisos a nivel de elemento basados en centros mediante el SharePoint Group ID configurado | Lista de registros procesados, lista de configuración de centros |

### 12.2 Validación y procesamiento de documentos

1. FL01 crea una copia del documento Excel en la biblioteca de validación.
2. Los usuarios autorizados de Site Owners o Site Members revisan y validan el documento.
3. El estado del documento cambia a `Validated`, lo que desencadena FL02.
4. FL02 interrumpe la herencia de permisos a nivel de archivo y asigna acceso de lectura a Site Owners y Site Members.
5. FL02 cambia el estado del documento de `Validated` a `Processing`.
6. FL02 procesa los datos de Excel y crea registros principales y asociados.
7. FL02 genera el libro de exportación correspondiente.
8. FL02 invoca FL04 para registrar los resultados y la información de diagnóstico.
9. FL02 cambia el estado del documento a `Processed` cuando el procesamiento y el registro requerido finalizan correctamente, o a `Error` si no pueden completarse satisfactoriamente.

El estado `Processing` indica que el documento está protegido y que el procesamiento está en curso. No garantiza que termine correctamente.

El estado final distingue entre procesamiento correcto (`Processed`) y errores de procesamiento o registro (`Error`).

### 12.3 Registro de ejecuciones y permisos

FL02 invoca FL04 para registrar la información del procesamiento.

FL04 crea una entrada en la lista de registros de ejecución y devuelve su identificador de elemento de SharePoint mediante el parámetro de salida `Id_Log`.

FL05 obtiene la correspondencia entre centros y grupos de la lista de configuración de centros y asigna permisos a nivel de elemento a los registros procesados correspondientes.

### 12.4 Limpieza de archivos temporales

FL03 elimina los archivos temporales de procesamiento con más de ocho horas de antigüedad, según la configuración conceptual de limpieza.

La limpieza debe limitarse a archivos temporales y no debe eliminar documentos de origen, registros validados ni exportaciones generadas.

### 12.5 Consideraciones de seguridad

Los flujos utilizan conexiones de SharePoint debidamente autorizadas.

Los permisos de automatización se administran por separado de los permisos de los usuarios ordinarios.

El personal externo no recibe acceso al subsitio de gestión a través del proceso de recepción de solicitudes.

Las definiciones detalladas de los flujos y las consideraciones de implementación se documentan en la sección de Power Automate del portfolio.

## 13. Seguridad y gobernanza

La solución sigue estos principios:

- Acceso de mínimo privilegio.
- Separación entre la recepción externa y la gestión interna.
- Acceso restringido a los documentos de validación.
- Protección de solo lectura de los documentos de origen procesados.
- Aislamiento de registros según el centro.
- Visibilidad restringida de los registros de ejecución.
- Configuración centralizada de las correspondencias entre centros y grupos.
- Control administrativo mediante Site Collection Administrators.
- Revisión de permisos efectivos para usuarios que pertenecen a varios grupos.

El formato JSON de SharePoint no se utiliza como mecanismo de seguridad.

La herencia de permisos y el acceso efectivo deben validarse en un entorno de pruebas antes del despliegue.

## 14. Documentación relacionada

- [Arquitectura de sitios de SharePoint](../architecture/sites-architecture.md)
- [Sitio principal de SharePoint](main-site.md)
- [Descripción general de SharePoint](../README.md)
- [Formato de la barra de comandos de bibliotecas documentales](../formatting/document-library-commandbar.json)

## 15. Estado de la documentación

**Estado:** documentación conceptual del subsitio de gestión completada.

Se han documentado los siguientes componentes:

- Estructura del subsitio de gestión y roles de usuario.
- Configuración de la biblioteca de validación y ciclo de protección documental.
- Configuración de la biblioteca de exportaciones y modelo de acceso.
- Estructura de la lista de registros procesados y permisos basados en centros.
- Lista de configuración de centros y correspondencias con grupos de SharePoint.
- Lista de registros de ejecución y seguimiento de ejecuciones.
- Estrategias de permisos a nivel de recurso y de elemento.
- Integración con los flujos FL01–FL05 de Power Automate.
- Consideraciones de seguridad y gobernanza.

### Validación de la implementación

Los siguientes aspectos requieren validación en un entorno de SharePoint de destino:

- Configuración del subsitio de gestión y pertenencia a grupos.
- Permisos efectivos de Site Owners, Site Members y Center Groups.
- Herencia de permisos de la biblioteca de validación y protección de documentos procesados.
- Permisos de acceso a la biblioteca de exportaciones.
- Asignaciones de permisos a nivel de elemento realizadas por FL05.
- Permisos y correspondencias entre centros y grupos de la lista de configuración de centros.
- Permisos de registro y comportamiento de ejecución de FL04.
- Conexiones de Power Automate y operaciones privilegiadas.
- Pruebas integrales de procesamiento, supervisión y seguridad.

Este documento describe una implementación conceptual y no constituye una plantilla de aprovisionamiento automatizado de SharePoint.
