# Automatización de SharePoint y Excel

## Descripción general del proyecto

Este proyecto presenta una arquitectura integral de procesamiento documental y automatización mediante Microsoft Power Platform, SharePoint Online, Excel Online y Office Scripts.

La solución automatiza el ciclo de vida de las solicitudes basadas en Excel: presentación, distribución de documentos, validación, tratamiento de datos, creación de registros, generación de exportaciones y gestión de permisos.

### Funcionamiento

1. **Presentación de solicitudes:** Los usuarios externos autorizados presentan solicitudes en una lista de SharePoint, adjuntando archivos Excel y observaciones.
2. **Distribución documental:** Power Automate recupera los adjuntos y crea copias en la biblioteca de documentos derivados del sitio principal y en la biblioteca de validación del subsitio de gestión.
3. **Validación:** Los usuarios de gestión autorizados revisan y validan los documentos antes de procesarlos.
4. **Procesamiento de Excel:** Power Automate y Office Scripts extraen y transforman los datos para crear registros principales y asociados en SharePoint.
5. **Generación de exportaciones:** La información procesada se incorpora a plantillas Excel estructuradas.
6. **Gestión de permisos:** Se aplican permisos a documentos y registros, incluidas restricciones de acceso por centro.
7. **Registro y mantenimiento:** Se registran las ejecuciones y se eliminan periódicamente los archivos temporales.

### Características de la arquitectura

- Arquitectura SharePoint de dos niveles: sitio principal y subsitio de gestión.
- Cinco flujos coordinados de Power Automate (FL01–FL05).
- Office Scripts para procesar datos estructurados de Excel.
- Listas y bibliotecas de SharePoint con permisos diferenciados.
- Seguridad dinámica por elemento basada en grupos configurables.
- Registro de ejecuciones y mantenimiento de archivos temporales.

### Alcance del portfolio

Se trata de una demostración independiente basada en un escenario empresarial ficticio. El repositorio contiene definiciones conceptuales de flujos, documentación técnica, ejemplos reutilizables de configuración y componentes de demostración desarrollados de forma independiente.

**No es una solución de producción completamente desplegable.** No incluye información confidencial, credenciales reales ni exportaciones propietarias de flujos.

## Tecnologías

- Microsoft Power Automate
- SharePoint Online
- Excel Online
- Office Scripts
- JSON
- API REST de SharePoint

## Escenario empresarial

### El problema

Una organización recibe archivos Excel estructurados de diferentes usuarios externos y centros operativos. Los documentos deben validarse, procesarse y convertirse en registros, con una distribución controlada de la información.

Sin automatización, el proceso puede requerir recopilación y organización manual de documentos, extracción repetitiva de datos, creación manual de registros, mantenimiento complejo de relaciones entre registros, administración de permisos por centro y seguimiento limitado de resultados y errores.

### La solución

La arquitectura centraliza la recepción de solicitudes en SharePoint y coordina las operaciones mediante Power Automate y Office Scripts. Incluye un proceso estandarizado de presentación, distribución documental automatizada, validación controlada, extracción y transformación de datos, creación secuencial de registros principales y asociados, exportaciones Excel, permisos por centro, registros centralizados de ejecución y limpieza programada.

### Beneficios previstos

- Reducir tareas administrativas repetitivas.
- Mejorar la coherencia del procesamiento documental.
- Conservar las relaciones entre registros vinculados.
- Facilitar el acceso controlado a información operativa sensible.
- Mejorar la trazabilidad de las operaciones.
- Simplificar el mantenimiento mediante configuración centralizada.
- Ofrecer una base ampliable para futuras mejoras.

Estos beneficios son **objetivos de diseño**, no resultados medidos en un entorno de producción.

## Componentes de la solución

### Flujos de Power Automate

| Componente | Función |
|---|---|
| **FL01 - Recepción de solicitudes** | Recupera los Excel adjuntos, distribuye copias en dos bibliotecas y gestiona permisos de la solicitud. |
| **FL02 - Procesamiento de Excel** | Procesa documentos validados, protege los originales, crea registros principales y asociados y genera libros de exportación. |
| **FL03 - Limpieza de archivos temporales** | Elimina de forma programada archivos temporales de procesamiento con más de ocho horas de antigüedad. |
| **FL04 - Registro de ejecuciones** | Crea registros de ejecución y devuelve el identificador `Id_Log` al flujo que lo invoca. |
| **FL05 - Permisos dinámicos** | Asigna permisos por elemento de SharePoint según la configuración de correspondencia entre centros y grupos. |

### Tecnologías de apoyo

| Componente | Función |
|---|---|
| SharePoint Online | Recepción de solicitudes, bibliotecas, listas estructuradas y permisos. |
| Excel Online | Almacenamiento e integración de libros con el procesamiento automatizado. |
| Office Scripts | Tratamiento de datos Excel y cumplimentación de plantillas de exportación. |
| API REST de SharePoint | Operaciones avanzadas, como ruptura de herencia y asignación de roles. |
| JSON | Intercambio de datos, definiciones conceptuales de flujos y personalización de interfaces. |

### Coordinación de componentes

**FL01** recibe y distribuye solicitudes. **FL02** procesa documentos validados y coordina la creación de registros y exportaciones. **FL04** centraliza el registro de ejecuciones, mientras que **FL05** aplica permisos por centro a los registros procesados. **FL03** ejecuta de manera independiente la limpieza programada de archivos temporales.

Esta separación permite distinguir responsabilidades de recepción, procesamiento, seguridad, seguimiento y mantenimiento.

## Aspectos técnicos destacados

### Orquestación con Power Automate

- Flujos basados en eventos de listas y documentos de SharePoint.
- Condiciones de desencadenador para controlar las ejecuciones.
- Coordinación entre varios procesos automatizados.
- Integración de un flujo secundario para centralizar registros.
- Automatizaciones programadas para la limpieza temporal.
- Separación modular entre procesamiento, permisos y registro.

### Automatización de Excel con Office Scripts

- Integración de Power Automate con Excel Online.
- Ejecución de scripts para tratar datos estructurados.
- Intercambio de datos JSON entre scripts y flujos.
- Procesamiento secuencial para preservar relaciones entre registros principales y asociados.
- Cumplimentación automática de plantillas de exportación.

### Arquitectura y seguridad de SharePoint

- Sitio principal y subsitio de gestión.
- Listas y bibliotecas con permisos diferenciados.
- Gestión de herencia de permisos a nivel de archivo.
- Permisos dinámicos por elemento mediante API REST.
- Correspondencia centro-grupo en una lista centralizada.
- Separación entre permisos de usuarios y privilegios de conexiones de automatización.

### Datos y trazabilidad

- Creación de registros principales y asociados.
- Conservación de relaciones mediante identificadores.
- Registro de ejecuciones con un flujo secundario específico.
- Seguimiento de estados y notificación de errores.
- Limpieza programada de documentos temporales.

### Principios reutilizables

Arquitectura modular, configuración centralizada, separación de responsabilidades, mínimo privilegio, mantenibilidad, extensibilidad y documentación técnica basada en datos ficticios.

Estas capacidades se ilustran mediante definiciones conceptuales, documentación y componentes de demostración seleccionados.

## Componentes y documentación del proyecto

### Flujos de Power Automate

Cada flujo dispone de un README técnico y un ejemplo JSON de su arquitectura conceptual.

| Flujo | Responsabilidad | Documentación | JSON conceptual |
|---|---|---|---|
| FL01 - Recepción de solicitudes | Solicitudes SharePoint, distribución de adjuntos y permisos | [README](flows/FL01-demo/README.md) | [JSON](flows/FL01-demo/FL01-demo.json) |
| FL02 - Procesamiento de Excel | Validación, datos, registros y exportaciones | [README](flows/FL02-demo/README.md) | [JSON](flows/FL02-demo/FL02-demo.json) |
| FL03 - Limpieza temporal | Eliminación de archivos temporales de más de ocho horas | [README](flows/FL03-demo/README.md) | [JSON](flows/FL03-demo/FL03-demo.json) |
| FL04 - Registro de ejecuciones | Registro centralizado y devolución de `Id_Log` | [README](flows/FL04-demo/README.md) | [JSON](flows/FL04-demo/FL04-demo.json) |
| FL05 - Permisos dinámicos | Permisos por elemento según el centro | [README](flows/FL05-demo/README.md) | [JSON](flows/FL05-demo/FL05-demo.json) |

**Importante:** Los JSON describen estructuras conceptuales y **no son paquetes de flujos directamente importables en Power Automate**.

### Office Scripts

Los scripts desarrollados en TypeScript ilustran el procesamiento de datos estructurados y la generación de libros Excel mediante Power Automate.

| Script | Responsabilidad | Código |
|---|---|---|
| `process-records.ts` | Lee registros estructurados y prepara datos para operaciones posteriores. | [Ver script](office-scripts/process-records.ts) |
| `populate-export-template.ts` | Cumplimenta una plantilla Excel predefinida con los datos procesados. | [Ver script](office-scripts/populate-export-template.ts) |

#### Integración con FL02

1. Lectura de datos de los libros validados.
2. Preparación de información para crear registros.
3. Transformación al formato de salida previsto.
4. Cumplimentación de plantillas Excel de exportación.

La creación de registros SharePoint, la asignación de permisos y el registro de ejecuciones corresponden a los flujos de Power Automate.

Los scripts son componentes independientes de demostración y pueden necesitar adaptaciones a las estructuras de libros, conexiones y entornos de destino.

[Explorar Office Scripts](office-scripts/)

### Archivos Excel de ejemplo

| Archivo | Finalidad | Enlace |
|---|---|---|
| `sample-records.xlsx` | Datos ficticios estructurados para procesar. | [Ver archivo](sample-data/sample-records.xlsx) |
| `export-template.xlsx` | Plantilla de libro Excel para ilustrar la exportación. | [Ver archivo](sample-data/export-template.xlsx) |

#### Papel en el proceso

1. Se adjunta un Excel estructurado a la lista de recepción de solicitudes.
2. FL01 distribuye las copias entre bibliotecas.
3. Los usuarios autorizados validan el documento.
4. FL02 coordina el procesamiento mediante Office Scripts.
5. Los datos se utilizan para crear registros SharePoint y completar un libro de exportación.

Los datos son ficticios. Los libros representan los conceptos de entrada y salida y pueden requerir ajustes para adaptarse a los scripts, estructuras y entornos concretos.

[Explorar datos de ejemplo](sample-data/)

### Documentación técnica

Diagramas de arquitectura, notas de implementación y documentación complementaria.

[Explorar documentación](docs/)

### Arquitectura y configuración de SharePoint

La arquitectura contempla un sitio principal y un subsitio de gestión, que cubren la recepción, validación, procesamiento, registros, permisos y seguimiento.

#### Arquitectura de sitios

| Componente | Documentación |
|---|---|
| Descripción de SharePoint | [Ver documentación](Sharepoint/README.md) |
| Arquitectura de sitios | [Ver arquitectura](Sharepoint/architecture/sites-architecture.md) |
| Sitio principal | [Ver sitio](Sharepoint/sites/main-site.md) |
| Subsitio de gestión | [Ver subsitio](Sharepoint/sites/management-subsite.md) |

#### Listas de SharePoint

| Lista | Función | Documentación |
|---|---|---|
| Lista de recepción de solicitudes | Recibe solicitudes y adjuntos Excel de usuarios externos autorizados. | [Ver](Sharepoint/lists/request-intake.md) |
| Lista de registros procesados | Almacena registros principales y asociados con permisos por centro. | [Ver](Sharepoint/lists/processed-records.md) |
| Lista de configuración de centros | Mantiene la correspondencia entre centros y grupos de SharePoint. | [Ver](Sharepoint/lists/centers-configuration.md) |
| Lista de registros de procesamiento | Guarda información de ejecuciones y diagnósticos. | [Ver](Sharepoint/lists/processing-logs.md) |

#### Bibliotecas documentales

| Biblioteca | Función | Documentación |
|---|---|---|
| Documentos derivados | Acceso de solo lectura a copias documentales en el sitio principal. | [Ver](Sharepoint/libraries/derived-documents.md) |
| Validación | Documentos pendientes de validar y protección de los ya procesados. | [Ver](Sharepoint/libraries/validation-library.md) |
| Archivos temporales | Libros temporales utilizados durante el procesamiento, limpiados por FL03 tras ocho horas. | [Ver](Sharepoint/libraries/temporary-files-library.md) |
| Exportaciones | Almacena los libros Excel de exportación generados. | [Ver](Sharepoint/libraries/export-library.md) |

#### Personalizaciones JSON

| Componente | Función | Ejemplo |
|---|---|---|
| Formulario de solicitudes | Diseño conceptual del formulario de observaciones y adjuntos. | [Ver JSON](Sharepoint/forms/request-intake-form.json) |
| Barra de comandos de solicitudes | Personalización de los comandos disponibles en la lista. | [Ver JSON](Sharepoint/formatting/request-intake-view.json) |
| Barra de comandos de bibliotecas | Simplificación de determinados comandos de biblioteca. | [Ver JSON](Sharepoint/formatting/document-library-commandbar.json) |

#### Seguridad y alcance

La documentación muestra permisos a nivel de sitio y recurso, protección de archivos procesados, control de acceso por centro a nivel de elemento, configuración centralizada de grupos y separación entre recepción externa y gestión interna.

Son patrones de arquitectura y configuración reutilizables, **no un paquete de aprovisionamiento SharePoint completamente desplegable**.

## Estructura del repositorio

```text
SharePoint-Excel-Automation/
|
|-- README.md
|
|-- flows/
|   |-- FL01-demo/
|   |-- FL02-demo/
|   |-- FL03-demo/
|   |-- FL04-demo/
|   `-- FL05-demo/
|
|-- office-scripts/
|   |-- process-records.ts
|   `-- populate-export-template.ts
|
|-- sample-data/
|   |-- sample-records.xlsx
|   `-- export-template.xlsx
|
|-- docs/
|   `-- Documentación técnica
|
`-- Sharepoint/
    |-- README.md
    |-- architecture/
    |-- sites/
    |-- lists/
    |-- libraries/
    |-- forms/
    `-- formatting/
```

### Función de cada carpeta

| Carpeta | Contenido |
|---|---|
| `flows/` | Definiciones conceptuales de flujos y documentación técnica. |
| `office-scripts/` | Scripts TypeScript para procesamiento de datos Excel y exportaciones. |
| `sample-data/` | Archivos Excel ficticios y plantillas. |
| `docs/` | Documentación técnica y notas de arquitectura. |
| `Sharepoint/` | Sitios, listas, bibliotecas, permisos, formularios y ejemplos JSON. |

### Navegación

- [Flujos de Power Automate](flows/)
- [Office Scripts](office-scripts/)
- [Archivos Excel de ejemplo](sample-data/)
- [Documentación técnica](docs/)
- [Arquitectura de SharePoint](Sharepoint/)

La estructura separa la lógica de automatización, los scripts, los datos de ejemplo y la configuración de SharePoint para facilitar la lectura y el mantenimiento.

## Estado del proyecto

**Estado: arquitectura conceptual y documentación técnica completadas.**

### Documentación completada

- Cinco flujos de Power Automate (FL01–FL05).
- Definiciones JSON conceptuales de cada flujo.
- Dos Office Scripts desarrollados de forma independiente.
- Datos ficticios de entrada y plantillas Excel de exportación.
- Arquitectura de sitio principal y subsitio de gestión.
- Cuatro listas y cuatro bibliotecas documentales de SharePoint.
- Ejemplos JSON de formato de listas y formularios.
- Estrategias de permisos por documento y elemento.
- Configuración de accesos por centro.
- Registros de procesamiento y limpieza temporal.
- Documentación técnica y navegación del repositorio.

### Alcance de implementación

El repositorio contiene definiciones conceptuales de flujos, scripts de apoyo, archivos ficticios y documentación de arquitectura. Los JSON no son flujos importables directamente y el proyecto no se distribuye como solución Power Platform o paquete de aprovisionamiento SharePoint completamente desplegable.

### Posibles mejoras futuras

- Crear flujos ejecutables de demostración en un entorno de pruebas.
- Validar los Office Scripts con los archivos Excel incluidos.
- Probar permisos con grupos ficticios de usuarios.
- Incorporar despliegue automatizado y configuración por entorno.
- Añadir capturas de ejecuciones y escenarios de demostración.
- Documentar pruebas integrales de extremo a extremo.
- Ampliar los ejemplos de tratamiento de errores y monitorización.

### Objetivo del portfolio

Demostrar competencias de arquitectura de soluciones, orquestación de Power Automate, diseño de seguridad en SharePoint, automatización de Excel y documentación técnica.

Todos los ejemplos tienen fines de aprendizaje, demostración y presentación profesional.

## Seguridad y confidencialidad

Este repositorio es una demostración independiente para aprendizaje, documentación técnica y portfolio profesional.

### Protección de datos

Los ejemplos utilizan escenarios genéricos y datos ficticios. La documentación pública no incluye intencionadamente:

- Datos personales o de clientes reales.
- Credenciales corporativas o tokens de autenticación.
- Direcciones SharePoint de producción o identificadores reales de tenant.
- Identificadores reales de grupos SharePoint o enlaces administrativos internos.
- Exportaciones propietarias de flujos o lógica empresarial confidencial.
- Documentos o conjuntos de datos internos de organizaciones.

### Componentes independientes de demostración

La arquitectura y los ejemplos se presentan como materiales desarrollados de manera independiente. Los JSON conceptuales de Power Automate no son exportaciones directas de un entorno corporativo de producción. Los Office Scripts y demás ejemplos pueden necesitar adaptaciones antes de utilizarse en otro entorno.

### Seguridad en SharePoint

El diseño ilustra:

- Principio de mínimo privilegio.
- Separación de recursos externos e internos.
- Gestión de permisos por documento y elemento.
- Restricciones de acceso por centro.
- Control administrativo mediante roles autorizados de SharePoint.
- Separación de permisos de usuario y privilegios de las conexiones de automatización.

**El formato JSON de SharePoint personaliza la interfaz, pero no constituye un mecanismo de seguridad.**

### Responsabilidad de implementación

Antes de adaptar la solución a un entorno real, los administradores deben revisar permisos y pertenencia a grupos; validar conexiones y autenticación; sustituir valores ficticios por configuraciones autorizadas; probar ejecuciones y errores; comprobar requisitos de protección de datos y cumplimiento; y verificar los permisos efectivos con cuentas de prueba específicas.

### Aviso

**Este repositorio no proporciona una solución lista para producción ni completamente desplegable.** Todas las descripciones de arquitectura, definiciones de flujos y configuraciones se ofrecen para demostrar conceptos técnicos y patrones de diseño reutilizables.
