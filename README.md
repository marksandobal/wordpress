# wordpress en docker

## setup

* Antes de continuar con la instalacion, por favor, crea los archivos de certificado para el ssl
para windows
```bash
    mkdir certs && cd certs

openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout mitienda.local.key \
  -out mitienda.local.crt \
  -subj "/C=US/ST=Local/L=Local/O=Dev/OU=Dev/CN=mitienda.local"

```

* para linux, corre lo siguiente

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout certs/mitienda.local.key 
  -out certs/mitienda.local.crt
  -subj "/CN=mitienda.local"
```
* mitienda.local es el nombre de tu pagina de wordpress o como decidas nombrar tu dominio
* Genera un dhparam.pem para fortalecer la seguridad

```bash
openssl dhparam -out dhparam.pem 2048
```

* agrega el dominio de tu tiemda o mitienda.local al archivo de hosts en /etc/hosts

nano /etc/host en linux o abre en un archivo de texto o IDE  en windows C:\Windows\System32\drivers\etc\hosts

agrega esta linea al final del archivo

```bash
127.0.0.1 mitienda.local
```

** Run docker-compose up -d
