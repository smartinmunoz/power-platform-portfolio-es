# Biblioteca de archivos temporales

## 1. Descripción general

La biblioteca de archivos temporales (*Temporary Files Library*) es una biblioteca de documentos de SharePoint Online ubicada en el subsitio de gestión.

Proporciona una ubicación específica para almacenar los libros de Excel temporales generados durante el procesamiento automatizado.

Los flujos de Power Automate FL02 y FL03 utilizan esta biblioteca para separar los archivos de trabajo de los documentos originales y de los archivos de exportación definitivos.

Todos los recursos descritos en este documento son conceptuales y utilizan información ficticia.

## 2. Configuración de la biblioteca

| Propiedad | Valor |
|---|---|
| Nombre de la biblioteca | Temporary Files Library |
| Ubicación en SharePoint | Subsitio de gestión |
| Tipo de recurso | Biblioteca de documentos |
| Finalidad principal | Procesamiento temporal de Excel |
| Flujo principal | FL02 - Procesamiento de Excel |
| Flujo de mantenimiento | FL03 - Limpieza de archivos temporales |
| Umbral de conservación | 8 horas |
| Modelo de acceso | Restringido a cuentas autorizadas de procesamiento y administración |

## 3. Finalidad funcional

La biblioteca proporciona un espacio de trabajo aislado para el procesamiento automatizado de Excel.

Sus principales objetivos son:

- Evitar que los archivos temporales de procesamiento se mezclen con los documentos de origen validados.
- Separar las copias de trabajo de los documentos de exportación definitivos.
- Facilitar la ejecución de Office Scripts mediante Power Automate.
- Reducir la acumulación innecesaria de documentos.
- Simplificar el mantenimiento automatizado y el diagnóstico de incidencias.

## 4. Integración con FL02

FL02 utiliza la biblioteca de archivos temporales durante el procesamiento de Excel.

### Secuencia de procesamiento

1. FL02 detecta un documento Excel validado en la biblioteca de validación.
2. El flujo recupera el libro de origen.
3. Se crea una copia temporal de trabajo en la biblioteca de archivos temporales.
4. Office Scripts procesa el libro temporal.
5. La información extraída se utiliza para crear registros en SharePoint.
6. Los libros de resultados definitivos se almacenan en la biblioteca de exportaciones.
7. Los archivos temporales de trabajo quedan sujetos a la limpieza automatizada de FL03.

El libro original validado permanece separado de las operaciones temporales de procesamiento.

## 5. Integración con FL03

FL03 se encarga de eliminar los archivos temporales que superan el umbral de conservación configurado.

### Secuencia de limpieza

1. FL03 se inicia según la programación de recurrencia configurada.
2. El flujo obtiene los archivos de la biblioteca de archivos temporales.
3. Cada archivo se evalúa con respecto al umbral de conservación.
4. Se seleccionan para su eliminación los archivos con más de 8 horas de antigüedad.
5. El flujo intenta eliminar cada archivo que cumple los criterios.
6. Los errores de eliminación se registran con fines de diagnóstico.

### Política de conservación

| Propiedad | Configuración |
|---|---|
| Umbral de conservación | 8 horas |
| Mecanismo de limpieza | Power Automate FL03 |
| Frecuencia de ejecución | Por definir durante la implementación |
| Alcance de la eliminación | Solo archivos temporales de procesamiento |
| Gestión de errores | Registro de diagnóstico y revisión posterior |

El umbral de ocho horas representa la antigüedad mínima para que un archivo pueda eliminarse, no una hora de eliminación garantizada.

## 6. Modelo de seguridad

La biblioteca de archivos temporales está restringida a usuarios y cuentas de automatización autorizados.

### Matriz de permisos

| Rol | Acceso |
|---|---|
| Site Owners | Control total |
| Miembros de gestión autorizados | Según las necesidades de administración |
| Conexión de procesamiento de Power Automate | Lectura / Escritura / Eliminación |
| Center Groups | Sin acceso |
| Personal externo | Sin acceso |

### Consideraciones de seguridad

- Restringir el acceso a los libros temporales.
- Evitar conceder acceso a usuarios externos o pertenecientes a grupos de centros.
- Aplicar el principio de mínimo privilegio a las conexiones de automatización.
- Proteger los archivos temporales que contengan información empresarial.
- Revisar los permisos efectivos y los accesos heredados.
- Asegurar que el flujo de limpieza dispone de los permisos necesarios para eliminar los archivos que cumplen los criterios.

La biblioteca no debe utilizarse para el almacenamiento permanente de documentos.

## 7. Relación con otras bibliotecas

| Biblioteca | Finalidad |
|---|---|
| Biblioteca de documentos derivados | Almacena copias distribuidas a partir de las solicitudes recibidas |
| Biblioteca de validación | Almacena documentos pendientes de validación o en proceso de validación |
| Biblioteca de archivos temporales | Almacena libros temporales utilizados durante el procesamiento |
| Biblioteca de exportaciones | Almacena los libros de resultados generados |

Cada biblioteca tiene una responsabilidad diferenciada dentro del ciclo de vida documental.

## 8. Consideraciones operativas

### Bloqueo de archivos

Las operaciones de Excel Online pueden bloquear temporalmente los libros.

FL03 debe gestionar los errores de eliminación provocados por bloqueos de archivos activos sin interrumpir todo el proceso de limpieza.

### Procesamiento simultáneo

Los nombres de los archivos temporales deben ser únicos para evitar conflictos entre ejecuciones simultáneas de los flujos.

### Errores de procesamiento

Los archivos temporales que permanezcan en la biblioteca tras una ejecución fallida seguirán siendo candidatos a la limpieza programada.

### Seguridad de la conservación

FL03 selecciona archivos temporales para eliminarlos cuando tienen más de ocho horas de antigüedad.

El flujo no comprueba explícitamente si un archivo sigue siendo necesario para una operación de procesamiento activa.

Los errores de eliminación, incluidos los causados por bloqueos activos de archivos Excel, se gestionan mediante mensajes de diagnóstico.

Antes de desplegar el flujo en un entorno de producción, debe revisarse si el umbral de ocho horas es adecuado para la duración prevista de los procesos.

## 9. Componentes relacionados

- [Arquitectura de SharePoint](../architecture/sites-architecture.md)
- [Biblioteca de validación](validation-library.md)
- [Biblioteca de exportaciones](export-library.md)
- [FL02 - Procesamiento de Excel](../../flows/FL02-demo/README.md)
- [FL03 - Limpieza de archivos temporales](../../flows/FL03-demo/README.md)

## 10. Estado de implementación

Este documento describe la arquitectura prevista para una biblioteca de SharePoint de demostración.

La configuración es conceptual y no se proporciona como un paquete desplegable de aprovisionamiento de SharePoint.

Los permisos reales, el comportamiento de conservación, las conexiones de automatización y las medidas de protección durante la limpieza deben validarse en un entorno de pruebas de Microsoft 365.
