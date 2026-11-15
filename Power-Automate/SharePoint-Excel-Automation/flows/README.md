# Flujos de Power Automate

Esta carpeta está destinada a flujos de demostración desarrollados de forma independiente, basados en un escenario genérico de procesamiento de Excel.

## Flujos de la solución

| Flujo | Descripción |
|---|---|
| FL01 | Recepción de archivos y distribución de documentos en SharePoint |
| FL02 | Procesamiento de Excel, generación de exportaciones y creación de registros en SharePoint |
| FL03 | Limpieza programada de archivos temporales |
| FL04 | Flujo secundario para registrar ejecuciones y realizar el seguimiento de errores |
| FL05 | Asignación dinámica de permisos a nivel de registro en SharePoint |

## Arquitectura

FL01 prepara los documentos para su validación manual.

FL02 procesa los archivos validados y crea los registros correspondientes en SharePoint.

FL03 realiza de forma independiente la limpieza de archivos temporales según una programación diaria.

FL04 es invocado por FL02 para registrar los resultados del procesamiento y los errores.

FL05 aplica permisos dinámicos cuando se crean registros en SharePoint.

## Estado del proyecto

Los flujos de demostración están en preparación para su implementación independiente.

La documentación técnica está disponible en la carpeta `docs`.

## Seguridad

Solo se publicarán datos ficticios, configuraciones genéricas y componentes desarrollados de forma independiente.

No se incluirán exportaciones de soluciones corporativas, credenciales, direcciones URL internas ni información confidencial.
