# LogRotate

## Installation logrotate – rotar logs de apache con logrotate.

- La herramienta logrotate ya se encuentra preconfigurada e instalada en la distribución Debian de Linux, de este modo en la mayoría de los casos nos podríamos saltar este paso.

```console
$ apt-get update
$ apt-get install logrotate
```

- Verificamos que disponemos de todos los ficheros y carpetas necesarias para su correcto funcionamiento:

```directory
/var/lib/logrotate/
/etc/logrotate.d/
/usr/sbin/logrotate
/etc/logrotate.conf
```

## Configurar logrotate – rotar logs de apache con logrotate.

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

- Crearemos el fichero para rotar logs de apache con logrotate en la distribución Debian de Linux:

```console
vi /etc/logrotate.d/apache2
```

- Y le añadiremos el siguiente contenido al fichero creado anteriormente:

```console
# ubicación de los logs del servidor web apache
/var/log/apache2/*.log {
        weekly
        missingok
        rotate 52
        compress
        delaycompress
        notifempty
        create 640 root adm
        sharedscripts
        postrotate
                if [ -f /var/run/apache2.pid ]; then
                        /etc/init.d/apache2 restart > /dev/null
                fi
        endscript
}

# logs de acceso y errores de los sitios web
/var/www/logs/*.log {
        weekly
        missingok
        rotate 52
        compress
        notifempty
        create 640 root root
        sharedscripts
        postrotate
                if [ -f /var/run/apache2.pid ]; then
                        /etc/init.d/apache2 restart > /dev/null
                fi
        endscript
}
```

- Ejecutar logrotate – rotar logs de apache con logrotate
- Posteriormente comprobaremos manualmente el correcto funcionamiento de logrotate usando el siguiente comando:

```console
/usr/sbin/logrotate /etc/logrotate.conf -f
```

- Además, logrotate debe ir configurado en un cron para que se ejecute periódicamente, esto podremos hacerlo gracias a la herramienta crontab (ejecuta procesos o scripts a intervalos regulares):

```console
crontab -e
```

- Y agregamos en nuestro archivo crontab el siguiente contenido:

```conf
# Rotar logs de apache con logrotate a las 3 am
0 03 * * * root /usr/sbin/logrotate /etc/logrotate.conf > /dev/null 2>&1
```

- Finalmente reiniciaremos el proceso cron para que los cambios surtan efecto:

```console
/etc/init.d/cron restart
```
