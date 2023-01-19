# LogRotate

## 1. Installation logrotate – rotar logs de MongoDB con logrotate.

- La herramienta logrotate ya se encuentra preconfigurada e instalada en la distribución Debian de Linux, de este modo en la mayoría de los casos nos podríamos saltar este paso.

```console
$ apt-get update
$ apt-get install logrotate
```

## 2. Verificamos que disponemos de todos los ficheros y carpetas necesarias para su correcto funcionamiento:

```directory
/var/lib/logrotate/
/etc/logrotate.d/
/usr/sbin/logrotate
/etc/logrotate.conf
```

## Configurar logrotate – rotar logs de MongoDB con logrotate.

- Para configurar nuestro logrotate actualizaremos el fichero logrotate.conf:

```console
vi /etc/logrotate.conf
```

- El contenido de nuestro logrotate.conf será similar al mostrado posteriormente:

```conf
# ejecutar "man logrotate" para más información
# rotar log semanalmente
weekly
# mantener logs durante 4 semanas
rotate 4
# rotar y crear nuevo log aunque esté vació el anterior
create
# descomentar si quieres comprimir logs
#compress
# ubicación de paquetes para el rotado de logs
include /etc/logrotate.d
# los logs wtmp o btmp los haremos rotar aquí
/var/log/wtmp {
    missingok
    monthly
    create 0664 root utmp
    rotate 1
}

/var/log/btmp {
    missingok
    monthly
    create 0664 root utmp
    rotate 1
}

# los logs del sistema se pueden rotar aquí
```

## 3. Crearemos el fichero para rotar logs de mongodb con logrotate en la distribución Debian de Linux

```console
# nano /etc/logrotate.d/mongodb
```

- Y le añadiremos el siguiente contenido al fichero creado anteriormente:

```console
/mongodb/log/mongod.log {
    daily
    rotate 15
    compress
    dateext
    notifempty
    copytruncate
    missingok
    }
```

- **weekly**. Rota los registros de MongoDB cada semana.
- **daily**. Rota los registros de MongoDB diario.
- **rotate 15**. Mantiene hasta 15 copias rotadas.
- **compress**. Comprime las copias rotadas.
- **dateext**. Agrega una extensión de fecha al nombre del archivo comprimido, para indicar cuándo fue comprimido.
- **notifempty** indica que el archivo de registro debe rotar incluso si está vacío
- **copytruncate**. indica que el archivo de registro debe copiarse y luego vaciado en lugar de moverse.
- **missingok**. indica que si el archivo de registro no existe, logrotate debe continuar sin errores.

## Ejecutar logrotate – rotar logs de mongodb con logrotate

- Posteriormente comprobaremos manualmente el correcto funcionamiento de logrotate usando el siguiente comando:

```console
# sudo logrotate -f /etc/logrotate.d/mongodb
```

- Además, logrotate debe ir configurado en un cron para que se ejecute periódicamente, esto podremos hacerlo gracias a la herramienta crontab:

```console
# crontab -e
```

- Y agregamos en nuestro archivo crontab el siguiente contenido:

```conf
# Rotar logs de mongodb con logrotate a las 12 am
0 0 * * * root logrotate -f /etc/logrotate.d/mongodb
```

- Finalmente reiniciaremos el proceso cron para que los cambios surtan efecto:

```console
# cron restart
```
