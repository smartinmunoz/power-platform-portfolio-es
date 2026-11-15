# Documentación técnica

Este directorio sirve como índice de la documentación técnica del proyecto de demostración de automatización de SharePoint y Excel.

El proyecto presenta una solución conceptual de Microsoft Power Platform que integra Power Automate, SharePoint Online, Excel Online y Office Scripts.

Todos los recursos utilizan datos ficticios y configuraciones genéricas.

## Arquitectura de la solución

La solución incluye cinco flujos conceptuales de Power Automate, dos Office Scripts y una estructura de SharePoint documentada.

### Arquitectura de SharePoint

- [Descripción general de SharePoint](../Sharepoint/README.md)
- [Arquitectura de sitios](../Sharepoint/architecture/sites-architecture.md)
- [Sitio principal](../Sharepoint/sites/main-site.md)
- [Subsitio de gestión](../Sharepoint/sites/management-subsite.md)

### Flujos de Power Automate

| Flujo | Descripción | Documentación |
|---|---|---|
| FL01 | Recepción de documentos y gestión de permisos | [FL01](../flows/FL01-demo/README.md) |
| FL02 | Procesamiento de Excel y creación de registros en SharePoint | [FL02](../flows/FL02-demo/README.md) |
| FL03 | Limpieza programada de archivos temporales | [FL03](../flows/FL03-demo/README.md) |
| FL04 | Registro centralizado de ejecuciones de procesamiento | [FL04](../flows/FL04-demo/README.md) |
| FL05 | Permisos dinámicos a nivel de registro | [FL05](../flows/FL05-demo/README.md) |

Estos flujos son demostraciones conceptuales, no definiciones de flujos importables directamente en Power Automate.

### Office Scripts

- [Procesamiento de registros de Excel](../office-scripts/process-records.ts)
- [Cumplimentación de la plantilla de exportación](../office-scripts/populate-export-template.ts)

Los scripts muestran cómo extraer datos de Excel y rellenar un libro de exportación.

### Datos de ejemplo

- [Registros de ejemplo](../sample-data/sample-records.xlsx)
- [Plantilla de exportación](../sample-data/export-template.xlsx)
- [Documentación de los datos de ejemplo](../sample-data/README.md)

### Componentes de SharePoint

La solución de SharePoint documentada incluye:

- Cuatro bibliotecas de documentos.
- Cuatro listas de SharePoint.
- Un ejemplo de configuración de formulario.
- Dos ejemplos de formato JSON.
- Documentación sobre la estructura de sitios y los permisos.

Consulta la [documentación de SharePoint](../Sharepoint/README.md) para conocer los detalles de cada componente.

## Alcance de la implementación

Este repositorio documenta una arquitectura de referencia ficticia.

Los archivos JSON de Power Automate describen estructuras conceptuales de flujos y no se pueden importar directamente en Power Automate.

Los Office Scripts se proporcionan como ejemplos en TypeScript.

Una implantación en producción requeriría configuración específica del entorno, autenticación de conectores, permisos, pruebas e implementación de las acciones reales de Power Automate.

## Seguridad y privacidad

- Registros e identificadores ficticios.
- Configuraciones genéricas de SharePoint.
- Sin información corporativa real.
- Sin credenciales ni secretos.
- Sin exportaciones de soluciones propietarias.

## Descripción general del proyecto

Volver al [README principal del proyecto](../README.md).
