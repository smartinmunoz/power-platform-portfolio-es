# FL05 - Permisos dinámicos de SharePoint

## Descripción general

Este flujo muestra la gestión dinámica de permisos a nivel de elemento en SharePoint Online.

Los permisos de acceso se determinan mediante una lista de configuración de SharePoint independiente, de modo que los distintos registros pueden asignarse a grupos de seguridad diferentes.

## Desencadenador

SharePoint: Cuando se crea un elemento.

El flujo se inicia automáticamente cada vez que se crea un registro nuevo en la lista de SharePoint de destino.

## Proceso principal

1. Detectar la creación de un elemento nuevo en una lista de SharePoint.
2. Recuperar el valor de referencia procedente de la primera columna del archivo Excel de origen.
3. Buscar ese valor en una lista de configuración de SharePoint independiente.
4. Identificar los grupos de seguridad asociados a dicha referencia.
5. Aplicar los permisos configurados al registro recién creado.

## Permisos basados en configuración

En lugar de definir de forma fija las asignaciones de grupos para cada registro, el flujo utiliza una lista de configuración independiente.

Ejemplo con datos ficticios:

| Referencia | Grupo de SharePoint |
|---|---|
| REGION-A | Group-A |
| REGION-B | Group-B |
| REGION-C | Group-C |

La lista de configuración determina qué grupos reciben acceso a cada registro.

## Tecnologías

- Power Automate
- SharePoint Online
- API REST de SharePoint
- Grupos de seguridad de SharePoint
- Contenido dinámico

## Competencias técnicas demostradas

- Automatización basada en eventos
- Asignación dinámica de permisos
- Flujos basados en configuración
- Consultas a listas de SharePoint
- Control de acceso a nivel de elemento
- Automatización de seguridad

## Seguridad

Este documento describe una arquitectura de demostración genérica.

Todos los valores de referencia y los nombres de grupos son ficticios.

No incluye URL corporativas, identificadores de grupos de seguridad, credenciales ni configuraciones internas de permisos.
