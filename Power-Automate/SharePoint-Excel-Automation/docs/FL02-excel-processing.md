# FL02 - Procesamiento de Excel y creación de registros

## Descripción general

Este flujo procesa archivos Excel validados manualmente, genera los archivos de salida necesarios y crea registros relacionados en SharePoint Online.

También gestiona los permisos de los documentos para evitar que los validadores modifiquen el archivo Excel durante el procesamiento.

## Desencadenador

SharePoint: Cuando se crea o modifica un archivo (solo propiedades).

El flujo utiliza la siguiente condición de desencadenamiento:

```text
@equals(triggerBody()?['Estat']?['Value'], 'Validat')
```

Solo los eventos en los que el estado del archivo es «Validado» pueden iniciar el flujo.

## Comprobación adicional del procesamiento

Antes de entrar en el bloque principal de procesamiento, el flujo vuelve a comprobar el estado de validación.

Esta condición adicional se introdujo como medida de protección durante las pruebas para evitar ejecuciones de procesamiento no previstas.

## Proceso principal

1. Desencadenar el flujo únicamente cuando el estado del archivo de SharePoint sea «Validado», mediante una condición de desencadenamiento.
2. Inicializar las variables de seguimiento de errores y el identificador del último registro principal.
3. Romper la herencia de permisos y cambiar los permisos de los validadores de Edición/Colaboración a Lectura.
4. Actualizar el estado del archivo a «Procesado» antes de iniciar las operaciones principales de tratamiento de datos.
5. Realizar una comprobación adicional del estado de validación para evitar procesamientos no previstos durante las pruebas.
6. Si se cumple la condición, recuperar el archivo Excel validado y crear una copia temporal de trabajo.
7. Ejecutar Office Scripts para extraer los registros de Excel y comprobar el resultado del script.
8. Generar los archivos Excel de salida necesarios, incluido el archivo preparado para importar datos en una aplicación externa.
9. Procesar los registros extraídos en Power Automate, distinguiendo entre registros principales (T) y asociados (B), y gestionar sus relaciones.
10. Crear los registros correspondientes en la lista de SharePoint de destino.

**Nota:** En la implementación actual, el estado «Procesado» se asigna antes de las operaciones principales. No confirma que el procesamiento haya finalizado correctamente.

## Procesamiento de registros

El archivo Excel de origen distingue dos tipos de registros:

- **T:** Registro principal.
- **B:** Registro asociado.

Los registros principales aparecen antes que sus registros asociados.

Power Automate gestiona la creación y las relaciones de estos registros en SharePoint. Esta lógica no corresponde al Office Script inicial que lee los datos de Excel.

### Procesamiento secuencial de registros

El bucle «Aplicar a cada uno» procesa los registros de Excel secuencialmente, con la concurrencia limitada a una iteración cada vez.

Esto mantiene el orden original y garantiza que cada registro principal (T) se cree antes que sus registros asociados (B).

Power Automate almacena en una variable el ID de SharePoint del registro principal creado más recientemente. Los registros asociados posteriores utilizan ese identificador para establecer la relación correcta.

La ejecución secuencial evita condiciones de carrera y asociaciones incorrectas provocadas por el procesamiento paralelo.

## Gestión de permisos

Antes de acceder al archivo y procesarlo, FL02 cambia los permisos de los validadores de colaboración a solo lectura.

De este modo, estos usuarios no pueden editar el archivo durante el procesamiento.

## Estado del procesamiento

- **Validado (`Validat`):** El archivo se ha revisado y aprobado manualmente para su procesamiento.
- **Procesado (`Tractat`):** El archivo ha entrado en la fase de procesamiento automatizado.

El flujo cambia el estado del archivo a «Procesado» antes de ejecutar las operaciones principales de tratamiento de datos.

Este estado no garantiza que el procesamiento haya terminado correctamente.

Los errores de procesamiento se registran por separado dentro del flujo.

## Archivos temporales

Office Scripts puede dejar archivos Excel temporales bloqueados durante un tiempo.

Un flujo programado independiente (FL03) se encarga de eliminar los archivos temporales con más de ocho horas de antigüedad.

## Tecnologías

- Power Automate
- SharePoint Online
- Excel Online (Business)
- Office Scripts
- TypeScript
- Gestión de permisos de SharePoint

## Competencias técnicas demostradas

- Automatización basada en metadatos
- Gestión del ciclo de vida documental
- Modificación de permisos antes del procesamiento
- Integración de Office Scripts
- Extracción de datos de Excel
- Generación de archivos de salida
- Creación de registros de SharePoint
- Procesamiento de registros relacionados
- Orquestación de flujos

## Seguridad

Este documento describe una arquitectura de demostración genérica.

No incluye URL corporativas, identificadores reales, datos personales, scripts propietarios ni configuraciones internas.

## Generación de exportaciones Excel

Después de procesar el libro de Excel de origen validado, el flujo genera un archivo Excel para una aplicación externa.

### Proceso

1. Power Automate recupera la plantilla Excel predefinida.
2. Power Automate crea un libro nuevo copiando la plantilla.
3. Power Automate envía los registros extraídos por el primer Office Script al segundo Office Script como una cadena JSON.
4. El Office Script `populate-export-template.ts` se ejecuta sobre el libro recién creado.
5. El script escribe los campos seleccionados en las celdas de destino predefinidas, a partir de la fila 15, y conserva los encabezados de la plantilla.
6. El script devuelve a Power Automate el número de registros escritos.
7. Tras generar los archivos de salida necesarios, Power Automate continúa creando los registros de la lista de SharePoint.

### Archivos de demostración

- `sample-data/sample-records.xlsx`: Datos de origen de ejemplo.
- `sample-data/export-template.xlsx`: Plantilla Excel con encabezados predefinidos.
- `office-scripts/process-records.ts`: Extrae los registros de origen.
- `office-scripts/populate-export-template.ts`: Rellena el libro de exportación.

### Notas de implementación

La demostración utiliza registros ficticios y una asignación simplificada de cuatro columnas.

El libro de exportación se genera antes de que el flujo comience a crear registros en la lista de SharePoint de destino.

En la arquitectura de producción descrita, Power Automate gestiona la creación de archivos y la orquestación, mientras que Office Scripts realiza las transformaciones de los datos de Excel.
