# FL03 - Limpieza programada de archivos temporales

## Descripción general

Este flujo muestra el mantenimiento programado de archivos Excel temporales generados durante el procesamiento automatizado de datos.

Aborda los problemas de bloqueo de archivos que pueden producirse después de ejecutar Office Scripts.

## Desencadenador

Power Automate: Recurrencia.

El flujo se ejecuta automáticamente una vez al día.

## Proceso principal

1. Iniciar el proceso de limpieza programado.
2. Recuperar los archivos temporales de la ubicación de almacenamiento designada.
3. Comprobar la antigüedad de cada archivo.
4. Identificar los archivos con más de ocho horas de antigüedad.
5. Eliminar los archivos que cumplan los criterios de limpieza.

## Desafío técnico

Los archivos Excel procesados mediante Office Scripts pueden permanecer bloqueados temporalmente.

Intentar eliminarlos de inmediato puede generar errores en Power Automate e interferir con otros flujos.

## Solución

El proceso de limpieza se separa del flujo principal de procesamiento.

Un flujo programado elimina los archivos temporales después de un período de conservación definido, reduciendo los conflictos provocados por los bloqueos temporales.

## Tecnologías

- Power Automate
- Flujos de nube programados
- SharePoint Online
- Expresiones de fecha y hora
- Gestión de archivos

## Competencias técnicas demostradas

- Automatización programada
- Gestión del ciclo de vida de archivos temporales
- Filtrado por fecha
- Separación de flujos
- Fiabilidad operativa
- Gestión de bloqueos de archivos

## Seguridad

La demostración pública utilizará una ubicación de pruebas específica y archivos ficticios.

No incluirá rutas de almacenamiento corporativas, credenciales ni configuraciones internas.
