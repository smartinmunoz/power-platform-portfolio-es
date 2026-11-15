# FL04 - Registros de ejecución y seguimiento de errores

## Descripción general

FL04 es un flujo secundario reutilizable de Power Automate encargado de registrar los resultados y errores del procesamiento en SharePoint Online.

Centraliza los registros de ejecución y proporciona trazabilidad de las operaciones automatizadas de procesamiento de Excel.

## Desencadenador

Power Automate: Cuando se llama a un flujo.

FL04 es invocado por el flujo principal (FL02).

## Parámetros de entrada

El flujo secundario recibe de FL02 información sobre el procesamiento:

- Identificador de referencia del procesamiento.
- Nombre del archivo de origen.
- Número de registros leídos.
- Número de registros creados.
- Errores de procesamiento.
- Identificador de ejecución del flujo.

## Proceso principal

1. Recibir la información de procesamiento de FL02.
2. Preparar el resumen de ejecución.
3. Crear una entrada de registro en una lista de SharePoint.
4. Guardar los resultados del procesamiento y los detalles de los errores.
5. Conservar el identificador de ejecución para garantizar la trazabilidad.

## Registro en SharePoint

Una lista específica de SharePoint almacena el historial de ejecuciones.

Esta información permite:

- Revisar los resultados del procesamiento.
- Identificar ejecuciones fallidas o incompletas.
- Investigar errores de procesamiento.
- Comparar el número de registros leídos y creados.
- Facilitar la resolución de incidencias y la supervisión operativa.

## Arquitectura

FL04 es un flujo secundario de FL02.

Separar el registro de ejecuciones del flujo principal mejora la modularidad, el mantenimiento y la reutilización.

## Tecnologías

- Power Automate
- Flujos secundarios
- SharePoint Online
- JSON
- Gestión de errores

## Notas de implementación

La demostración pública utiliza datos ficticios y estructuras genéricas de listas de SharePoint.

El nombre del archivo de origen y los demás parámetros de entrada deben validarse para garantizar que las entradas de registro contienen los valores esperados.

## Seguridad

No se incluyen URL corporativas, identificadores de tenant, credenciales, datos personales ni configuraciones propietarias.
