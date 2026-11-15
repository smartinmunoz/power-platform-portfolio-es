# FL01 - Recepción y distribución de archivos Excel

## Descripción general

Este flujo automatiza la recepción y distribución controlada de archivos Excel en SharePoint Online.

Crea dos copias de cada archivo presentado en bibliotecas de documentos diferentes, con permisos distintos según su finalidad.

## Desencadenador

SharePoint: Cuando se crea un elemento.

El flujo se inicia al crear un elemento nuevo con un archivo Excel adjunto en una lista de SharePoint.

## Proceso principal

1. Detectar el elemento de lista de SharePoint recién creado.
2. Recuperar el archivo Excel adjunto.
3. Crear una copia en la biblioteca de documentos derivados del sitio principal.
4. Configurar los permisos de acceso de la copia de documentos derivados.
5. Crear una segunda copia en la biblioteca de validación del subsitio de gestión.
6. Configurar los permisos de los usuarios autorizados para revisar y validar el archivo.
7. Actualizar los metadatos de SharePoint y la información de procesamiento pertinentes.

## Bibliotecas de documentos

### Biblioteca de documentos derivados

Almacena una copia protegida del archivo Excel presentado en el sitio principal.

Solo el administrador del sitio de SharePoint está autorizado a modificar estos archivos.

### Biblioteca de validación

Almacena una copia independiente para su revisión y validación manual.

Los validadores autorizados tienen permisos de colaboración durante la fase de validación.

## Alcance del flujo

FL01 no ejecuta Office Scripts ni procesa el contenido del archivo Excel.

Su responsabilidad consiste en recibir y distribuir los archivos y configurar los permisos iniciales.

El procesamiento posterior lo gestiona FL02 tras la validación manual.

## Tecnologías

- Power Automate
- SharePoint Online
- Bibliotecas de documentos de SharePoint
- API REST de SharePoint
- Gestión de permisos a nivel de elemento y archivo

## Competencias técnicas demostradas

- Automatización basada en eventos
- Gestión de archivos adjuntos de SharePoint
- Distribución automatizada de archivos
- Gestión de permisos
- Separación entre almacenamiento documental y validación
- Ciclo de vida documental controlado

## Seguridad

Esta documentación describe una arquitectura de demostración genérica.

No incluye URL corporativas, identificadores reales de grupos, credenciales, datos personales ni código fuente propietario.
