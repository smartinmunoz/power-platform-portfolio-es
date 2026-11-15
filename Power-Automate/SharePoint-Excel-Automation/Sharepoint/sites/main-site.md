# Sitio principal de SharePoint

## 1. Descripción general

El sitio principal de SharePoint es el punto de entrada de una solución de recepción y procesamiento documental desarrollada con SharePoint Online y Power Automate.

Ofrece un entorno controlado donde el personal externo autorizado puede presentar solicitudes y acceder a los documentos generados que le correspondan.

Un subsitio de gestión permite realizar las operaciones internas de validación, procesamiento y supervisión.

> **Aviso sobre el portfolio:** Esta documentación utiliza nombres ficticios y describe una demostración conceptual. No contiene URL de producción, identificadores reales de grupos, credenciales ni información confidencial.

## 2. Estructura del sitio

```text
Sitio principal de SharePoint
|
|-- Lista de recepción de solicitudes
|   |-- Formulario personalizado de nuevo elemento
|   `-- Formato JSON de la barra de comandos
|
|-- Biblioteca de documentos derivados
|
`-- Subsitio de gestión
    `-- Procesamiento y administración internos
```

## 3. Acceso de usuarios

El sitio contempla distintos roles de usuario.

| Rol de usuario | Finalidad |
|---|---|
| Personal externo | Presentar solicitudes y acceder a documentos autorizados |
| Site Owners | Gestión y administración del sitio |
| Site Members | Operaciones internas |
| Administradores de la colección de sitios | Control administrativo sobre la colección de sitios |

El acceso a cada recurso se controla mediante permisos de SharePoint.

Los permisos concretos de cada lista y biblioteca se documentan por separado.

## 4. Lista de recepción de solicitudes

### 4.1 Finalidad

La lista de recepción de solicitudes proporciona un mecanismo estructurado para recopilar solicitudes del personal externo autorizado.

Los usuarios crean solicitudes mediante un formulario personalizado de SharePoint.

### 4.2 Presentación de solicitudes

La lista ofrece un proceso simplificado para el personal externo autorizado.

El proceso sigue estos pasos:

1. El usuario abre el formulario personalizado de nuevo elemento.
2. Opcionalmente, introduce información adicional en el campo `Observations`.
3. Adjunta un libro de Excel mediante la funcionalidad estándar de adjuntos de SharePoint.
4. Guarda la solicitud.
5. SharePoint crea el elemento de lista y le asigna un ID único.
6. FL01 recupera la información de la solicitud y su adjunto Excel.
7. FL01 crea una copia del documento en la biblioteca de documentos derivados del sitio principal.
8. FL01 crea otra copia independiente en la biblioteca de validación del subsitio de gestión.
9. FL01 aplica los permisos configurados al elemento de solicitud.

### Seguridad de las solicitudes

Tras la presentación, FL01 interrumpe la herencia de permisos del elemento de solicitud.

La persona que creó la solicitud conserva acceso de **lectura** a su solicitud.

Site Owners y Site Members reciben acceso de **lectura**, mientras que el resto del personal externo y los grupos de centros quedan excluidos.

### Distribución documental

La biblioteca de documentos derivados permite acceso de **lectura** a los usuarios autorizados del sitio principal, incluido el personal externo.

La biblioteca de validación está restringida a los usuarios de gestión autorizados y permite continuar el ciclo de validación y procesamiento.

La estructura detallada de las solicitudes, la configuración del formulario y los permisos se describen en la documentación de la lista de recepción de solicitudes.

### 4.3 Formulario personalizado

La lista utiliza la personalización de formularios JSON de SharePoint para ofrecer una interfaz simplificada.

El formulario se organiza en torno a dos campos:

- **Observations:** campo opcional de texto de varias líneas para información adicional.
- **Attachments:** funcionalidad estándar de adjuntos de listas de SharePoint, utilizada para enviar libros de Excel.

La configuración de demostración está disponible en el repositorio:

[Ver JSON del formulario de recepción de solicitudes](../forms/request-intake-form.json)

Este JSON representa un ejemplo conceptual de distribución del formulario. Debe comprobarse su compatibilidad con el control nativo de adjuntos de SharePoint en un entorno de demostración.

La personalización del formulario es independiente del formato de la barra de comandos y no sustituye los permisos ni las reglas de validación de SharePoint.

### 4.4 Formato de la barra de comandos

La lista utiliza formato JSON de SharePoint para personalizar los comandos disponibles.

La configuración de demostración se encuentra en:

`../formatting/request-intake-view.json`

Esta personalización ofrece una interfaz más centrada en la presentación de solicitudes.

> El formato de la barra de comandos mejora la usabilidad, pero no sustituye los permisos de seguridad de SharePoint.

## 5. Biblioteca de documentos derivados

### 5.1 Finalidad

La biblioteca de documentos derivados se encuentra en el sitio principal de SharePoint.

Almacena una copia de cada libro de Excel presentado, creada por FL01 durante la recepción documental.

Esta biblioteca proporciona un espacio de consulta para los usuarios autorizados del sitio principal.

### 5.2 Creación de documentos

FL01 crea la copia después de recuperar el adjunto Excel de la lista de recepción de solicitudes.

El flujo utiliza una conexión de SharePoint con los permisos adecuados para crear documentos en la biblioteca.

El adjunto original permanece asociado a su elemento de lista de SharePoint.

También se crea una copia independiente en la biblioteca de validación del subsitio de gestión.

### 5.3 Modelo de acceso

La biblioteca de documentos derivados proporciona acceso de **lectura** a los usuarios autorizados del sitio principal.

| Rol de usuario | Acceso |
|---|---|
| Administradores de la colección de sitios | Control administrativo |
| Site Owners | Lectura |
| Site Members | Lectura |
| Personal externo | Lectura |
| Grupos de centros | Según la asignación en el sitio principal |

Los usuarios ordinarios no necesitan permisos de edición para consultar los documentos.

Las operaciones de creación y administración documental se realizan mediante la conexión de automatización autorizada.

### 5.4 Personalización de la biblioteca

La solución incluye una demostración de personalización de la barra de comandos de bibliotecas de SharePoint.

El archivo JSON correspondiente se encuentra en:

`../formatting/document-library-commandbar.json`

El formato de la barra de comandos mejora la interfaz de usuario, pero no sustituye los permisos de seguridad de SharePoint.

### 5.5 Documentación relacionada

La configuración detallada de la biblioteca, el ciclo documental y las consideraciones de permisos se describen en:

[Biblioteca de documentos derivados](../libraries/derived-documents.md)

## 6. Integración con Power Automate

El sitio principal de SharePoint actúa como punto de entrada del ciclo automatizado de procesamiento documental.

La solución utiliza cinco flujos conceptuales de Power Automate.

### 6.1 Responsabilidades de los flujos

| Flujo | Responsabilidad | Recursos principales |
|---|---|---|
| FL01 | Recepción de solicitudes, recuperación de adjuntos, distribución documental y permisos de los elementos de solicitud | Lista de recepción de solicitudes, biblioteca de documentos derivados, biblioteca de validación |
| FL02 | Procesamiento de Excel validados, creación de registros, generación de exportaciones y protección de documentos procesados | Biblioteca de validación, lista de registros procesados, biblioteca de exportaciones |
| FL03 | Limpieza de archivos temporales | Archivos temporales de procesamiento |
| FL04 | Registro de ejecuciones y seguimiento de diagnósticos | Lista de registros de ejecución |
| FL05 | Asignación de permisos a nivel de elemento basada en centros | Lista de registros procesados, lista de configuración de centros |

### 6.2 Operaciones del sitio principal

El sitio principal permite la presentación inicial de solicitudes y la consulta de documentos.

1. El personal externo presenta un libro de Excel mediante la lista de recepción de solicitudes.
2. FL01 recupera el adjunto enviado.
3. FL01 crea una copia en la biblioteca de documentos derivados.
4. FL01 crea otra copia en la biblioteca de validación del subsitio de gestión.
5. FL01 aplica los permisos configurados al elemento de solicitud original.

La biblioteca de documentos derivados proporciona acceso de **lectura** a los usuarios autorizados del sitio principal.

### 6.3 Operaciones del subsitio de gestión

El subsitio de gestión se encarga del ciclo posterior de procesamiento interno.

- Los usuarios autorizados de gestión validan los documentos recibidos.
- FL02 procesa los archivos Excel validados y crea registros principales y asociados.
- FL02 genera libros de exportación y protege los documentos de origen procesados.
- FL04 registra la información de ejecución y devuelve el identificador `Id_Log`.
- FL05 asigna permisos a nivel de registro según el centro configurado.
- FL03 elimina archivos temporales de acuerdo con las reglas de limpieza establecidas.

### 6.4 Seguridad y conexiones de automatización

Los flujos de Power Automate utilizan conexiones con los permisos adecuados para realizar operaciones en SharePoint.

Los permisos de los usuarios ordinarios se mantienen separados de los que necesita la automatización.

El personal externo no obtiene acceso al subsitio de gestión por el hecho de presentar una solicitud.

Las definiciones y la documentación detallada de los flujos están disponibles en la sección de Power Automate del portfolio.

## 7. Consideraciones de seguridad

La arquitectura sigue estos principios:

- Los usuarios externos solo pueden acceder a los recursos autorizados.
- Los recursos de gestión están separados de la interfaz externa de recepción.
- Los permisos de SharePoint aplican las restricciones de acceso.
- El formato JSON no se considera una barrera de seguridad.
- Los administradores de la colección de sitios conservan el control administrativo.
- La documentación pública excluye identificadores de producción e información confidencial.

## 8. Documentación relacionada

- [Arquitectura de sitios de SharePoint](../architecture/sites-architecture.md)
- [Descripción general de SharePoint](../README.md)
- [Formato de barra de comandos de recepción de solicitudes](../formatting/request-intake-view.json)
- [Formato de barra de comandos de bibliotecas documentales](../formatting/document-library-commandbar.json)

## 9. Estado de la documentación

**Estado:** Documentación conceptual del sitio principal de SharePoint completada.

Se han documentado los siguientes componentes:

- Estructura del sitio principal y roles de usuario.
- Configuración de la lista de recepción de solicitudes y proceso de presentación.
- JSON del formulario personalizado de nuevo elemento.
- Formato JSON de la barra de comandos.
- Configuración de la biblioteca de documentos derivados y modelo de acceso.
- Integración con los flujos FL01 a FL05 de Power Automate.
- Permisos a nivel de elemento de las solicitudes.
- Distribución documental entre el sitio principal y el subsitio de gestión.
- Consideraciones de seguridad y conexiones de automatización.

### Validación de la implementación

Los siguientes aspectos requieren validación en un entorno de SharePoint de destino:

- Configuración del sitio principal y pertenencia a grupos.
- Permisos efectivos del personal externo, Site Owners y Site Members.
- Asignaciones de permisos a nivel de elemento realizadas por FL01.
- Funcionamiento de los adjuntos nativos de SharePoint en el formulario personalizado.
- Permisos de creación y acceso a documentos en la biblioteca de documentos derivados.
- Permisos de las conexiones de Power Automate y comportamiento durante la ejecución.
- Pruebas integrales de presentación de solicitudes y distribución documental.

Este documento describe una implementación conceptual y no constituye una plantilla de aprovisionamiento automatizado de sitios de SharePoint.
