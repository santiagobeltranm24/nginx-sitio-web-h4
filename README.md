# Administración, respaldo y migración de un servidor Docker con Nginx

Proyecto académico de **Sistemas Operativos — Hito 4**. El trabajo aborda la administración de un servidor web Nginx ejecutado en un contenedor Docker sobre Alpine Linux, incluyendo revisión del entorno, respaldo de información, exportación de una imagen y preparación para su migración a otro sistema Linux.

> **Importante:** los nombres de contenedores, etiquetas de imágenes, puertos y rutas dependen de la instalación utilizada durante la práctica. Los comandos de esta guía usan valores de ejemplo o variables; reemplázalos por los datos reales de tu entorno antes de ejecutarlos.

## Objetivo

Documentar un procedimiento reproducible para inspeccionar el servicio Nginx, respaldar sus archivos y conservar la imagen Docker necesaria para trasladar el servicio a otro equipo Linux.

## Tecnologías y herramientas

- **Alpine Linux:** sistema operativo del entorno de trabajo.
- **Docker:** administración de contenedores e imágenes.
- **Nginx:** servidor web.
- **WinSCP:** transferencia de archivos entre equipos.
- **GitHub:** almacenamiento de la documentación del proyecto.

## Alcance de la práctica

1. Inspeccionar los contenedores y las imágenes disponibles.
2. Revisar el estado del contenedor, la configuración, los puertos publicados, los registros y el consumo de recursos.
3. Evaluar límites de recursos a nivel de Docker sin modificar los recursos asignados a la máquina virtual.
4. Copiar los archivos del sitio web desde el contenedor hacia el sistema Alpine para conservar un respaldo.
5. Construir y exportar una imagen Docker personalizada.
6. Explicar el traslado de los respaldos y la imagen a otro equipo Linux.
7. Documentar la transferencia de archivos mediante WinSCP.

## Comandos de inspección

Consultar los contenedores activos y todos los contenedores:

```bash
docker ps
docker ps -a
```

Consultar las imágenes disponibles:

```bash
docker images
```

Inspeccionar un contenedor:

```bash
docker inspect <nombre_o_id_del_contenedor>
docker logs <nombre_o_id_del_contenedor>
docker stats <nombre_o_id_del_contenedor>
```

Revisar los puertos publicados:

```bash
docker port <nombre_o_id_del_contenedor>
```

## Respaldo de los archivos del sitio

Crear un directorio de respaldo en Alpine Linux:

```bash
mkdir -p ~/H4_Apellido_Nombre/respaldo-sitio
```

Copiar los archivos desde el contenedor al sistema anfitrión. Sustituir la ruta de origen por la ubicación real de los archivos de Nginx dentro del contenedor:

```bash
docker cp <contenedor>:/ruta/real/del/sitio ~/H4_Apellido_Nombre/respaldo-sitio/
```

Comprobar que los archivos se hayan copiado:

```bash
ls -lah ~/H4_Apellido_Nombre/respaldo-sitio/
```

## Construcción y exportación de la imagen

Desde el directorio que contiene el Dockerfile, construir una imagen personalizada:

```bash
docker build -t nginx-h4:1.0 .
```

Comprobar que la imagen exista:

```bash
docker images
```

Exportar la imagen a un archivo TAR:

```bash
docker save -o ~/H4_Apellido_Nombre/nginx-h4.tar nginx-h4:1.0
```

El archivo generado contiene la imagen Docker. No sustituye el respaldo de archivos o volúmenes que puedan contener datos persistentes.

## Migración a otro equipo Linux

1. Transferir el directorio de respaldo y el archivo TAR al equipo de destino, por ejemplo, mediante WinSCP.
2. Verificar que Docker esté instalado y funcionando en el equipo de destino.
3. Importar la imagen:

```bash
docker load -i nginx-h4.tar
```

4. Comprobar que la imagen se haya importado:

```bash
docker images
```

5. Restaurar o montar los archivos del sitio según la configuración original y crear el contenedor con los puertos y parámetros que correspondan al entorno de destino.
6. Verificar el funcionamiento del servicio mediante el navegador y revisar los registros del contenedor.

La imagen conserva el entorno empaquetado, pero la configuración de ejecución —por ejemplo, puertos, volúmenes y variables— debe reproducirse al crear el nuevo contenedor.

## Transferencia con WinSCP

Utilizar WinSCP para conectarse al equipo Alpine mediante el protocolo habilitado en el entorno (por ejemplo, SFTP). Transferir la carpeta de trabajo `H4_Apellido_Nombre` al equipo local y comprobar que incluya los respaldos y archivos solicitados para la entrega.

## Organización sugerida de la entrega

```text
H4_Apellido_Nombre/
├── respaldo-sitio/
├── nginx-h4.tar
├── evidencias/
└── informe/
```

La estructura es orientativa: conservar los nombres y archivos exigidos por la consigna de la asignatura.

## Consideraciones

- Los comandos con valores entre `< >` son plantillas y deben adaptarse a los nombres y rutas reales.
- No se deben publicar contraseñas, claves privadas, tokens ni datos sensibles.
- Los respaldos y las imágenes Docker pueden ocupar bastante espacio; GitHub se utiliza aquí para documentar el trabajo, no como sustituto del almacenamiento de respaldos.
- Antes de dar por válida una migración, comprobar que el sitio web responda correctamente en el equipo de destino.

## Autoría

Trabajo académico realizado para la asignatura de **Sistemas Operativos**.

