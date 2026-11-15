# Lista de recepción de solicitudes

## 1. Descripción general

La lista de recepción de solicitudes (*Request Intake List*) es una lista de SharePoint Online ubicada en el sitio principal de SharePoint.

Proporciona una interfaz sencilla para que el personal externo autorizado presente documentos Excel destinados a su posterior validación y procesamiento automatizado.

La solución busca reducir la introducción manual de datos y simplificar la presentación de solicitudes.

> **Aviso sobre el portfolio:** Esta documentación describe una demostración ficticia. No incluye direcciones URL de producción, identificadores reales, credenciales ni información empresarial confidencial.

## 2. Configuración de la lista

| Propiedad | Configuración |
|---|---|
| Nombre de la lista | `Request Intake` |
| Ubicación | Sitio principal de SharePoint |
| Tipo de lista | Lista de SharePoint Online |
| Archivos adjuntos | Habilitados |
| Interfaz de usuario | Formulario personalizado de SharePoint |
| Automatización | Power Automate |
| Finalidad principal | Presentación de documentos Excel |

La lista utiliza funcionalidades estándar de SharePoint y personalizaciones de interfaz basadas en JSON.

## 3. Columnas de la lista

El formulario de presentación solo requiere un campo personalizado y la funcionalidad estándar de archivos adjuntos de SharePoint.

| Campo | Tipo | Finalidad |
|---|---|---|
| `Observations` | Varias líneas de texto | Comentarios o información opcional proporcionada por la persona solicitante |
| `Attachments` | Archivos adjuntos de lista de SharePoint | Cargar el documento Excel que se procesará |

La solución también utiliza campos estándar del sistema de SharePoint.

| Campo del sistema | Finalidad |
|---|---|
| `ID` | Identificador único del elemento de la lista |
| `Created` | Fecha y hora de presentación |
| `Created By` | Usuario que creó la solicitud |
| `Modified` | Fecha y hora de la última modificación |
| `Modified By` | Usuario que realizó la última modificación |

La columna predeterminada `Title` puede existir en la lista subyacente de SharePoint, pero en esta demostración no es necesario que el usuario la cumplimente.

## 4. Proceso de presentación de solicitudes

El usuario sigue un proceso sencillo:

1. Abre la lista de recepción de solicitudes.
2. Selecciona la opción para crear una solicitud nueva.
3. Introduce observaciones cuando sea necesario.
4. Adjunta el libro de Excel.
5. Guarda la solicitud.

El elemento creado y su archivo adjunto quedan disponibles para el flujo de Power Automate configurado.

## 5. Formulario personalizado de SharePoint

La lista utiliza personalización de formularios mediante JSON para ofrecer una interfaz simplificada de presentación de documentos Excel.

El formulario se organiza alrededor de dos campos:

- **Observaciones:** campo opcional de varias líneas de texto para proporcionar información adicional.
- **Archivos adjuntos:** funcionalidad estándar de SharePoint para adjuntar libros Excel a los elementos de la lista.

### Configuración del formulario

El archivo JSON de demostración está disponible en el repositorio:

[Ver JSON del formulario de recepción de solicitudes](../forms/request-intake-form.json)

La configuración organiza la información de la solicitud en una sección denominada `Request Information`.

Este ejemplo es conceptual. El comportamiento del control nativo de archivos adjuntos dentro del diseño JSON del formulario debe comprobarse en un entorno de demostración.

### Personalización del formulario y de la barra de comandos

La personalización del formulario y el formato de la barra de comandos cumplen funciones diferentes:

- **JSON del formulario:** define la disposición y organización del formulario de solicitud.
- **JSON de la barra de comandos:** personaliza los comandos disponibles en la lista y su presentación.
- **Permisos de SharePoint:** controlan qué usuarios pueden crear, leer, editar o administrar elementos de la lista.

La personalización de la interfaz mediante JSON no sustituye los permisos de seguridad de SharePoint ni la validación del lado del servidor.

## 6. Personalización de la barra de comandos

La lista utiliza formato JSON para simplificar las acciones disponibles en la barra de comandos.

La configuración de demostración se encuentra en:

[Ver JSON de la barra de comandos de recepción de solicitudes](../formatting/request-intake-view.json)

La personalización puede cambiar el nombre de los comandos u ocultarlos para mejorar la experiencia del usuario.

**Importante:** Ocultar comandos mediante formato JSON no impide que los usuarios realicen acciones para las que sigan teniendo permisos en SharePoint.

## 7. Gestión de archivos adjuntos

La lista utiliza la funcionalidad estándar de archivos adjuntos de SharePoint.

El formato documental previsto es Excel.

El flujo de Power Automate se encarga de recuperar y procesar el archivo adjunto presentado.

Las validaciones de archivos adjuntos, incluidos el tipo de archivo, la cantidad de archivos y la estructura del contenido, deben implementarse por separado cuando sean necesarias.

La presencia de un campo de archivos adjuntos no restringe automáticamente las cargas a documentos Excel.

## 8. Permisos y seguridad

La lista se encuentra en el sitio principal de SharePoint y permite al personal externo autorizado presentar documentos Excel para su procesamiento.

### Permisos de presentación de solicitudes

El personal externo debe disponer de los permisos de SharePoint necesarios para crear elementos de solicitud y cargar archivos adjuntos.

El formulario personalizado simplifica la presentación, pero no controla los permisos subyacentes de SharePoint.

### Permisos de los elementos de solicitud

FL01 aplica permisos a nivel de elemento para proteger cada solicitud presentada.

El modelo conceptual de permisos es el siguiente:

| Rol | Acceso |
|---|---|
| Administradores de la colección de sitios | Control administrativo |
| Persona que creó la solicitud | Lectura |
| Site Owners | Lectura |
| Site Members | Lectura |
| Resto del personal externo | Sin acceso |
| Grupos de centros | Sin acceso |

Tras la presentación, FL01 interrumpe la herencia de permisos del elemento y asigna los permisos de lectura necesarios.

La persona que creó la solicitud puede consultarla sin recibir permisos ordinarios de edición.

Site Owners y Site Members pueden consultar las solicitudes presentadas según el modelo de seguridad configurado.

### Seguridad de los archivos adjuntos

Los archivos Excel adjuntos pertenecen al elemento de lista correspondiente y deben protegerse mediante sus permisos efectivos.

FL01 recupera el archivo Excel adjunto mediante una conexión de SharePoint debidamente autorizada.

El flujo crea copias documentales independientes en:

- La biblioteca de documentos derivados del sitio principal de SharePoint.
- La biblioteca de validación del subsitio de gestión.

Cada destino aplica su propio modelo de seguridad.

### Consideraciones de seguridad

- Aplicar el principio de mínimo privilegio.
- Garantizar que el personal externo pueda presentar solicitudes sin acceder a las de otros usuarios.
- Comprobar que la herencia de permisos se interrumpe correctamente a nivel de elemento.
- Impedir modificaciones no autorizadas de las solicitudes presentadas.
- Validar los permisos efectivos de usuarios pertenecientes a varios grupos de SharePoint.
- No utilizar la personalización JSON del formulario o de la barra de comandos como mecanismo de seguridad.
- Probar el proceso completo de presentación y asignación de permisos en un entorno controlado de SharePoint.

## 9. Integración con Power Automate

La lista de recepción de solicitudes es el punto de entrada de la solución de procesamiento documental automatizado.

FL01 se inicia cuando se crea un elemento de solicitud en el sitio principal de SharePoint.

### FL01: recepción y distribución de documentos

El flujo sigue esta secuencia conceptual:

1. Un usuario autorizado crea una solicitud en la lista de recepción de solicitudes.
2. Introduce observaciones opcionales y adjunta un libro de Excel.
3. SharePoint crea el elemento de solicitud y le asigna un `ID` único.
4. FL01 recupera la información de la solicitud y su archivo Excel adjunto.
5. FL01 crea una copia en la biblioteca de documentos derivados del sitio principal de SharePoint.
6. FL01 crea otra copia en la biblioteca de validación del subsitio de gestión.
7. FL01 aplica los permisos correspondientes y actualiza los metadatos necesarios de la solicitud y los documentos.

### Destinos de los documentos

| Destino | Ubicación | Finalidad |
|---|---|---|
| Biblioteca de documentos derivados | Sitio principal de SharePoint | Consulta de solo lectura por usuarios autorizados del sitio principal, incluido el personal externo |
| Biblioteca de validación | Subsitio de gestión | Validación documental interna por Site Owners y Site Members autorizados |

Las dos copias documentales tienen finalidades distintas y diferentes permisos de acceso.

### Procesamiento posterior

Una vez validado el documento en la biblioteca de validación, FL02 inicia el flujo de procesamiento de Excel.

La solución también incluye:

- FL03 para la limpieza de archivos temporales.
- FL04 para el registro de ejecuciones.
- FL05 para la asignación dinámica de permisos por centro a los registros.

La lógica detallada de los flujos se documenta en la sección de Power Automate del portfolio.

### Consideraciones de seguridad

FL01 debe utilizar una conexión de SharePoint debidamente autorizada para crear copias documentales y administrar permisos.

El personal externo no debe obtener acceso a la biblioteca de validación restringida del subsitio de gestión.

La biblioteca de documentos derivados proporciona acceso de solo lectura a los usuarios autorizados del sitio principal de SharePoint.

El elemento de solicitud y sus archivos adjuntos deben permanecer protegidos mediante permisos de SharePoint.

## 10. Ejemplo de presentación

El siguiente ejemplo utiliza información ficticia.

| Campo | Ejemplo |
|---|---|
| `Observations` | Envío mensual de datos para revisión |
| Archivo adjunto | `sample-request.xlsx` |

El ejemplo muestra la experiencia de usuario prevista sin utilizar información empresarial real.

## 11. Documentación relacionada

- [Sitio principal de SharePoint](../sites/main-site.md)
- [Arquitectura de sitios de SharePoint](../architecture/sites-architecture.md)
- [Descripción general de SharePoint](../README.md)
- [JSON de la barra de comandos de recepción de solicitudes](../formatting/request-intake-view.json)

## 12. Estado de la documentación

**Estado:** documentación conceptual de la lista completada.

Se han documentado los siguientes componentes:

- Estructura y configuración de la lista de recepción de solicitudes.
- Campo `Observations` y archivos adjuntos estándar de SharePoint.
- JSON del formulario personalizado para crear elementos.
- Formato JSON de la barra de comandos.
- Proceso de presentación de solicitudes.
- Integración con Power Automate FL01.
- Consideraciones generales de seguridad y permisos.

### Validación de la implementación

Los siguientes aspectos requieren validación o configuración en un entorno de SharePoint de destino:

- Comportamiento del control nativo de archivos adjuntos dentro del formulario JSON personalizado.
- Permisos efectivos a nivel de lista y elemento.
- Reglas de validación de adjuntos Excel, incluidos el tipo de archivo y la cantidad de archivos.
- Permisos de las conexiones de Power Automate.
- Pruebas integrales de la presentación de solicitudes y la ejecución de FL01.

La documentación describe una implementación conceptual y no proporciona una plantilla automatizada de aprovisionamiento de listas de SharePoint.
