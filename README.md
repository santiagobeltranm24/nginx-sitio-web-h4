# Nginx con Docker — Hito 4

Proyecto académico de **Sistemas Operativos — Hito 4**. Este repositorio contiene el respaldo de archivos de configuración y del sitio web de Nginx utilizado en la práctica de administración, respaldo y migración de un servidor Docker sobre Alpine Linux.

## Contenido del repositorio

```text
nginx-sitio-web-h4/
├── README.md
└── backup/
    ├── conf.d/
    │   └── default.conf
    └── html/
        ├── 50x.html
        └── index.html
```

### Archivos respaldados

- `backup/conf.d/default.conf`: configuración del bloque `server` de Nginx. Escucha en el puerto 80, utiliza `/usr/share/nginx/html` como raíz del sitio y define la página de error `50x.html` para los errores 500, 502, 503 y 504.
- `backup/html/index.html`: página principal personalizada en español. Presenta la práctica del Hito 4 y menciona Alpine Linux, Docker y Nginx.
- `backup/html/50x.html`: página de error de Nginx incluida en el respaldo.

## Objetivo

Conservar los archivos relevantes del sitio web y su configuración para facilitar su revisión, respaldo y restauración en otro entorno Linux.

## Tecnologías y herramientas

- **Alpine Linux:** entorno Linux utilizado en la práctica.
- **Docker:** administración de contenedores e imágenes.
- **Nginx:** servidor web que publica el contenido estático.
- **WinSCP:** transferencia de archivos entre equipos.
- **GitHub:** control de versiones y documentación del respaldo.

## Inspección del contenedor

Los siguientes comandos permiten revisar el estado del entorno. Sustituye `CONTENEDOR` por el nombre o ID real:

```bash
docker ps -a
docker images
docker inspect CONTENEDOR
docker logs CONTENEDOR
docker stats CONTENEDOR
docker port CONTENEDOR
```

## Respaldo de archivos desde el contenedor

En la configuración incluida, Nginx sirve el sitio desde `/usr/share/nginx/html` y carga la configuración del servidor desde `/etc/nginx/conf.d/default.conf`. Si esas rutas coinciden con el contenedor utilizado, los archivos se pueden copiar con:

```bash
mkdir -p backup/html backup/conf.d
docker cp CONTENEDOR:/usr/share/nginx/html/. ./backup/html/
docker cp CONTENEDOR:/etc/nginx/conf.d/default.conf ./backup/conf.d/default.conf
```

Verifica el contenido copiado:

```bash
find backup -type f -maxdepth 3
```

## Exportación y restauración de una imagen Docker

Para guardar una imagen existente, primero identifica su nombre y etiqueta con `docker images`. Después:

```bash
docker save -o nginx-h4.tar NOMBRE_IMAGEN:ETIQUETA
```

En el equipo Linux de destino, después de transferir el archivo TAR:

```bash
docker load -i nginx-h4.tar
docker images
```

El archivo TAR de la imagen no incluye automáticamente los datos que estén guardados en volúmenes ni reemplaza el respaldo de los archivos del sitio. Conserva y transfiere ambos por separado cuando corresponda.

## Restauración del sitio

Los archivos respaldados pueden utilizarse al crear un nuevo contenedor, montándolos en las rutas que utiliza Nginx. Ejemplo orientativo:

```bash
docker run -d --name nginx-restaurado \
  -p 8080:80 \
  -v "$(pwd)/backup/html:/usr/share/nginx/html:ro" \
  -v "$(pwd)/backup/conf.d/default.conf:/etc/nginx/conf.d/default.conf:ro" \
  NOMBRE_IMAGEN:ETIQUETA
```

Adapta el nombre de la imagen y el puerto a tu entorno. Si modificas la configuración, valida que Nginx pueda iniciarse correctamente y revisa los registros:

```bash
docker logs nginx-restaurado
```

## Transferencia con WinSCP

Transfiere la carpeta del proyecto al equipo local mediante el protocolo habilitado en el entorno (por ejemplo, SFTP). Comprueba que `backup/` incluya la configuración y los archivos HTML necesarios.

## Notas

- Los comandos son ejemplos de referencia; ejecútalos solo después de confirmar nombres, rutas, etiquetas y puertos reales.
- Este repositorio contiene los archivos del respaldo y su documentación. No contiene una imagen Docker exportada ni un Dockerfile en la estructura actual.
- GitHub sirve para versionar los archivos de texto y la documentación; no sustituye el respaldo externo de imágenes, volúmenes o evidencias que solicite la actividad.
- No subas contraseñas, tokens, claves privadas ni otros datos sensibles.

## Autoría

Trabajo académico realizado para la asignatura de **Sistemas Operativos**.