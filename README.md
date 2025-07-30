# wordpress en docker

###  Eres libre de modificar el archivo docker si no cumple con lo que necesitas.

## Sigue las instrucciones para la instalación en docker

## Instalación

1 - Antes de continuar con la instalación, por favor, crea los archivos de certificado para el ssl
para windows. Si estas en el CMD escribe los siguientes comandos.

>[!NOTE]
>Es posible que tengas problemas para correr los siguientes comandos en el CMD de windows. Te recomiendo que uses el bash de github, si estas usando windows.

* mitienda.local es el nombre de tu página de wordpress o como decidas nombrar tu dominio

* Crea la carpeta /certs
```bash
 $ mkdir certs
```

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout mitienda.local.key \
  -out mitienda.local.crt \
  -subj "/C=US/ST=Local/L=Local/O=Dev/OU=Dev/CN=mitienda.local"

```
En caso de que el comando anterior no funcione en bash de git, prueba directamente con el bash de Ubuntu en Windows (WSL). Recuerda que windows 10 y 11 tiene un subsistema de ubuntu. Solo tienes que activarlo.

2 - para linux, corre lo siguiente

```bash
$ mkdir certs

$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout certs/mitienda.local.key \
  -out certs/mitienda.local.crt \
  -subj "/CN=mitienda.local"

```

3 - El archivo **dhparam.pem** contiene los parámetros Diffie-Hellman (DH) que se usan para establecer conexiones seguras mediante el protocolo TLS/SSL. Genera un dhparam.pem para fortalecer la seguridad. Corre el siguiente comando en el bash de ubuntu o linux

```bash
$ openssl dhparam -out dhparam.pem 2048
```

>[!TIP]
>En caso de que el archivo se haya generado fuera de la carpeta /certs, muevelo dentro de la carpeta. En el docker-compose.yml se tiene la instrucción que los archivos están ubicados dentro de la carpeta /certs. Si no lo tienes asi, es posible que tengas errores al intentar usar el certificado ssl en el url de wordpress.

```yml
# docker-compose.yml
  nginx-proxy:
    ...
    volumes:
      - ./certs:/etc/nginx/certs:ro #<- Justo aquí muestras el path de los archivos generados anteriormente
      - ./certs/dhparam.pem:/etc/nginx/dhparam/dhparam.pem:ro # <- y aquí
      ...
```

4 - agrega el dominio de tu tiemda o mitienda.local al archivo de hosts en /etc/hosts
para linux


```bash
$ nano /etc/host
```

para windows abre en un archivo de texto o IDE  en windows C:\Windows\System32\drivers\etc\hosts

agrega esta linea al final del archivo

```bash
127.0.0.1 mitienda.local
```

5 - Crea la imagen y el contenedor con el siguiente comando de docker
```bash
$ docker-compose up -d
```

>[!NOTE]
> Si despues de crear la imagen y el contenedor no puedes ingresar con el url del dominio que hayas seleccionado, en este caso practico fue mitienda.local, revisa como wordpress registro el url del sitio y el home en la base de datos.

* Ingresa al bash del contenedor de la base de datos, pudes encontrar el nombre en el docker-compose en el servicio db, container_name

```bash
$ docker exec -it server-mysql bash

```
* Conectate como root

```bash
$ mysql -u root -p
```
* Recuerda que el password y el user de la base de datos se encuentra en la misma configuracion don el docker-compose.yml
      

```bash
  MYSQL_ROOT_PASSWORD: root
  MYSQL_DATABASE: wordpress
  MYSQL_USER: forge
  MYSQL_PASSWORD: secret
```

* Ingresa a la base de datos y en la tabla wp_options busca los campos siteurl y home
```SQL
>USE wordpress;

>SELECT option_name, option_value FROM wp_options WHERE option_name IN ('siteurl', 'home');
```

* Es posible que los valores se encuentren como https://localhost:8002 y http://localhost:8002. Tendras algo como esto:

| option_name | option_value          |
|-------------|-----------------------|
| home        | http://localhost:8002 |
| siteurl     | http://localhost:8002 |


* Actualizalos al dominio que usaste, si seguiste las instrucciones tal cual de esta instalación, entonces su dominio es igual a mitienda.local

```SQL
>UPDATE wp_options SET option_value = 'https://mitienda.local' WHERE option_name = 'siteurl';
>UPDATE wp_options SET option_value = 'https://mitienda.local' WHERE option_name = 'home';

```

* Luego reinicia el contenedor de wordpress

```SQL
$ docker restart wordpress

```

Ingresa nuevamente al url del dominio, deberias poder ver wordpress funcionando.
