# FL03 - Limpieza de archivos temporales

## Descripción general

Demostración conceptual de un flujo programado de Power Automate que elimina archivos temporales de SharePoint con más de 8 horas de antigüedad.

Todos los recursos e identificadores de SharePoint son ficticios.

## Arquitectura

### Diagrama del flujo

```mermaid
flowchart TD
    A["Desencadenador de recurrencia"] --> B["Obtener archivos temporales"]
    B --> C["Filtrar archivos con más de 8 horas"]
    C --> D{"¿Hay archivos que procesar?"}
    D -->|No| E["Finalizar"]
    D -->|Sí| F["Aplicar a cada archivo"]
    F --> G["Eliminar archivo temporal"]
    G -->|Correcto| H["Continuar procesamiento"]
    G -->|Error u omitido| I["Componer mensaje de diagnóstico"]
    I --> H
    H --> J{"¿Quedan archivos?"}
    J -->|Sí| F
    J -->|No| E
```

## Pasos del flujo

1. Ejecutarse según una programación recurrente.
2. Recuperar exclusivamente los archivos de la biblioteca independiente `Temporary Files Library`, ubicada en el subsitio de gestión.
3. Filtrar los archivos con más de 8 horas de antigüedad.
4. Recorrer los archivos filtrados.
5. Eliminar cada archivo seleccionado.
6. Generar un mensaje de diagnóstico si la eliminación falla o se omite.

## Política de retención

| Configuración | Valor |
|---|---|
| Umbral de antigüedad | Más de 8 horas |
| Destino | `Temporary Files Library` (subsitio de gestión) |
| Ejecución | Programación recurrente |
| Frecuencia exacta de recurrencia | No especificada |

## Relación con FL02 y las bibliotecas

FL02 crea las copias temporales de trabajo en `Temporary Files Library` para procesarlas mediante Office Scripts. Los documentos originales permanecen en la biblioteca de validación y los resultados definitivos se guardan en la biblioteca de exportaciones. FL03 se ejecuta de forma independiente y su limpieza se limita a la biblioteca temporal: **no debe eliminar archivos de validación, documentos derivados ni exportaciones**.

La antigüedad superior a ocho horas es el criterio conceptual de selección. Antes de implantarlo, debe verificarse que no existan ejecuciones activas que sigan necesitando los archivos y que la conexión de FL03 tenga permisos de eliminación solo donde corresponda.

Consulta [Biblioteca de archivos temporales](../../Sharepoint/libraries/temporary-files-library.md), [FL02](../FL02-demo/README.md) y [Arquitectura de sitios](../../Sharepoint/architecture/sites-architecture.md).

## Gestión de diagnósticos

Si una acción de eliminación falla o se omite, una acción **Componer (Compose)** genera un mensaje de diagnóstico que identifica el archivo afectado y sugiere comprobar si existe un bloqueo de Excel Online.

Se trata de una medida de diagnóstico, no de un sistema completo de registro de errores o notificaciones.

## Aspectos técnicos destacados

- Diseño de un flujo en la nube programado.
- Operaciones sobre bibliotecas de documentos de SharePoint.
- Filtrado de matrices según la antigüedad de los archivos.
- Procesamiento mediante **Aplicar a cada uno (Apply to each)**.
- Eliminación automatizada de archivos temporales.
- Ejecución condicional mediante **Configurar ejecución posterior (Run after)**.
- Mensajes de diagnóstico para operaciones fallidas u omitidas.

## Estado de implementación

**Demostración conceptual. No es un flujo importable de Power Automate.**

El archivo `FL03-demo.json` describe la arquitectura prevista y no contiene definiciones ejecutables de acciones de Power Automate.

## Seguridad

- Recursos de SharePoint ficticios.
- Sin datos reales de usuarios.
- Sin credenciales ni identificadores de conexión.
- Sin exportaciones de soluciones corporativas.

## Tecnologías

- Microsoft Power Automate
- SharePoint Online
- JSON
- Mermaid
