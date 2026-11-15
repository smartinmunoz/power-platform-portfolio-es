# Office Scripts

Esta carpeta contiene dos Office Scripts desarrollados para una demostración ficticia de automatización de SharePoint y Excel.

## Scripts disponibles

### process-records.ts

Lee los registros de la hoja de cálculo `Input`.

- Detecta dinámicamente los encabezados de las columnas.
- Extrae los registros del rango utilizado.
- Omite las filas completamente vacías.
- Devuelve los registros extraídos como una cadena JSON.

### populate-export-template.ts

Rellena una plantilla de exportación de Excel existente.

- Recibe los registros mediante un parámetro de tipo cadena JSON.
- Utiliza la hoja de cálculo `Export`.
- Escribe los campos seleccionados a partir de la celda B15.
- Conserva los encabezados predefinidos de la plantilla.
- Devuelve el número de registros escritos.

## Archivos de ejemplo

Ambos scripts se pueden probar con los archivos de la carpeta `sample-data`:

- `sample-records.xlsx`
- `export-template.xlsx`

## Integración con Power Automate

Power Automate orquesta el proceso, crea el libro de exportación a partir de una plantilla y transmite los datos de los registros entre los scripts.

La demostración utiliza una estructura de datos simplificada y registros ficticios.

## Seguridad

No se incluye código corporativo, credenciales, direcciones URL internas ni información confidencial.
