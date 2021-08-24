# wb11-mail

Tras la instalación obtenemos esta configuración
```
* URLs of installed web applications:
*
* - Roundcube webmail: https://mail.devops-alumno08.com/mail/
* - SOGo groupware: https://mail.devops-alumno08.com/SOGo/
* - netdata (monitor): https://mail.devops-alumno08.com/netdata/
*
* - Web admin panel (iRedAdmin): https://mail.devops-alumno08.com/iredadmin/
*
* You can login to above links with below credential:
*
* - Username: postmaster@devops-alumno08.com
* - Password: *****************
```
He creado el usuario enrique.sanz

## Configura let's encrypt.
> Los certificados autofirmados no són seguros. Mira como habilitar let's encrypt en todos los servicios en la documentación de iredmail.

He creado el certificado con certbot y lo he sustituido por los que ha creado iRedMail:
```
rm -f /etc/ssl/private/iRedMail.key
rm -f /etc/ssl/certs/iRedMail.crt
ln -s /etc/letsencrypt/live/mail.devops-alumno08.com/privkey.pem /etc/ssl/private/iRedMail.key
ln -s /etc/letsencrypt/live/mail.devops-alumno08.com/fullchain.pem /etc/ssl/certs/iRedMail.crt

service nginx restart
```


## Configura el DNS.

Creamos los registros SPF y DKIM:
 ![image](https://user-images.githubusercontent.com/65896169/126862629-6dc0a398-af21-4f78-be23-683bef0b156f.png)

Obtenemos las claves con:
	amavisd-new showkeys | sed 's/"//g'

Y copiamos lo que hay entre paréntesis en el registro:
 ![image](https://user-images.githubusercontent.com/65896169/126862632-93499c79-8839-4f09-91a8-3ac39b75a666.png)

Lo verificamos con:
	amavisd-new testkeys
	(TESTING#1 devops-alumno08.com: dkim._domainkey.devops-alumno08.com => pass)

## Pruebas
> Crea otra cuenta de email y envia de una a otra un correo con un adjunto de 100mb.

> Descarga un cliente de correo si no tienes, puedes usar thunderbird. Configura localmente las 2 cuentas y comprueba que todo funciona correctamente.

Incrementamos el tamaño de fichero a 100mb:
https://serverok.in/iredmail-increase-attachment-size#:~:text=It%20can%20be%20anything%20above,size%20you%20want%20to%20use.


Contenido de iRedMail.tips:

```
Admin of domain devops-alumno08.com:

    * Account: postmaster@devops-alumno08.com
    * Password: *************

    You can login to iRedAdmin with this account, login name is full email address.

First mail user:
    * Username: postmaster@devops-alumno08.com
    * Password: **************
    * SMTP/IMAP auth type: login
    * Connection security: STARTTLS or SSL/TLS

    You can login to webmail with this account, login name is full email address.

* Enabled services:  rsyslog postfix mysql nginx php7.3-fpm dovecot clamav-daemon amavis clamav-freshclam sogo memcached fail2ban cron nftables


SSL cert keys (size: 4096):
    - /etc/ssl/certs/iRedMail.crt
    - /etc/ssl/private/iRedMail.key

Mail Storage:
    - Mailboxes: /var/vmail/vmail1
    - Mailbox indexes:
    - Global sieve filters: /var/vmail/sieve
    - Backup scripts and backup copies: /var/vmail/backup

MySQL:
    * Root user: root, Password: "**************" (without quotes)
    * Bind account (read-only):
        - Username: vmail, Password: **************
    * Vmail admin account (read-write):
        - Username: vmailadmin, Password: **************
    * Config file: /etc/mysql/my.cnf
    * RC script: /etc/init.d/mysql

Virtual Users:
    - /root/iRedMail-1.4.0/samples/iredmail/iredmail.mysql
    - /root/iRedMail-1.4.0/runtime/*.sql

Backup MySQL database:
    * Script: /var/vmail/backup/backup_mysql.sh
    * See also:
        # crontab -l -u root

Postfix:
    * Configuration files:
        - /etc/postfix
        - /etc/postfix/aliases
        - /etc/postfix/main.cf
        - /etc/postfix/master.cf

    * SQL/LDAP lookup config files:
        - /etc/postfix/mysql

Dovecot:
    * Configuration files:
        - /etc/dovecot/dovecot.conf
        - /etc/dovecot/dovecot-ldap.conf (For OpenLDAP backend)
        - /etc/dovecot/dovecot-mysql.conf (For MySQL backend)
        - /etc/dovecot/dovecot-pgsql.conf (For PostgreSQL backend)
        - /etc/dovecot/dovecot-used-quota.conf (For real-time quota usage)
        - /etc/dovecot/dovecot-share-folder.conf (For IMAP sharing folder)
    * Syslog config file:
        - /etc/rsyslog.d/1-iredmail-dovecot.conf (present if rsyslog >= 8.x)
    * RC script: /etc/init.d/dovecot
    * Log files:
        - /var/log/dovecot/dovecot.log
        - /var/log/dovecot/sieve.log
        - /var/log/dovecot/lmtp.log
        - /var/log/dovecot/lda.log (present if rsyslog >= 8.x)
        - /var/log/dovecot/imap.log (present if rsyslog >= 8.x)
        - /var/log/dovecot/pop3.log (present if rsyslog >= 8.x)
        - /var/log/dovecot/sieve.log (present if rsyslog >= 8.x)
    * See also:
        - /var/vmail/sieve/dovecot.sieve
        - Logrotate config file: /etc/logrotate.d/dovecot

Nginx:
    * Configuration files:
        - /etc/nginx/nginx.conf
        - /etc/nginx/sites-available/00-default.conf
        - /etc/nginx/sites-available/00-default-ssl.conf
    * Directories:
        - /etc/nginx
        - /var/www/html
    * See also:
        - /var/www/html/index.html

php-fpm:
    * Configuration files: /etc/php/7.3/fpm/pool.d/www.conf

PHP:
    * PHP config file for Nginx:
    * Disabled functions: posix_uname,eval,pcntl_wexitstatus,posix_getpwuid,xmlrpc_entity_decode,pcntl_wifstopped,pcntl_wifexited,pcntl_wifsignaled,phpAds_XmlRpc,pcntl_strerror,ftp_exec,pcntl_wtermsig,mysql_pconnect,proc_nice,pcntl_sigtimedwait,posix_kill,pcntl_sigprocmask,fput,phpinfo,system,phpAds_remoteInfo,ftp_login,inject_code,posix_mkfifo,highlight_file,escapeshellcmd,show_source,pcntl_wifcontinued,fp,pcntl_alarm,pcntl_wait,ini_alter,posix_setpgid,parse_ini_file,ftp_raw,pcntl_waitpid,pcntl_getpriority,ftp_connect,pcntl_signal_dispatch,pcntl_wstopsig,ini_restore,ftp_put,passthru,proc_terminate,posix_setsid,pcntl_signal,pcntl_setpriority,phpAds_xmlrpcEncode,pcntl_exec,ftp_nb_fput,ftp_get,phpAds_xmlrpcDecode,pcntl_sigwaitinfo,shell_exec,pcntl_get_last_error,ftp_rawlist,pcntl_fork,posix_setuid

ClamAV:
    * Configuration files:
        - /etc/clamav/clamd.conf
        - /etc/clamav/freshclam.conf
        - /etc/logrotate.d/clamav
    * RC scripts:
            + /etc/init.d/clamav-daemon
            + /etc/init.d/clamav-freshclam

Amavisd-new:
    * Configuration files:
        - /etc/amavis/conf.d/50-user
        - /etc/postfix/master.cf
        - /etc/postfix/main.cf
    * RC script:
        - /etc/init.d/amavis
    * SQL Database:
        - Database name: amavisd
        - Database user: amavisd
        - Database password: **************

DNS record for DKIM support:

; key#1 2048 bits, i=dkim, d=devops-alumno08.com, /var/lib/dkim/devops-alumno08.com.pem
dkim._domainkey.devops-alumno08.com.	3600 TXT (
  "v=DKIM1; p="
  "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAlbIlX0XcylvE66pcoOOv"
  "sTGhEu+gsBEV3lc4/HguD3LoDaA1KnG9kPoHE+SQQ8gD/NwsTwDD0LzO0gODZ7k/"
  "TswZbzYi3YSV9o+ciCdFnur3kFQ7gK/bT1BVqbMt0zfY/GO1R36Xpy7PT8XoqMti"
  "oz1XI7jKscsEmWJwlSU0OoihBwdTRoi012zuwOM+QRNAd0t1N99/4iwBLwYlFSky"
  "omdCY6uH0Lne58XNVzFWnmTupeVOfRCrTu8yIOSgcGHaB/xw7dR+dsVtn06gFs+X"
  "ZisISqVlPrCbZIRdjTUYof3yg6YMq0VGMNnAtGyz0M1ZZEPrZ+raUfjU7nNEPniz"
  "TwIDAQAB")
SpamAssassin:
    * Configuration files and rules:
        - /etc/mail/spamassassin
        - /etc/mail/spamassassin/local.cf

iRedAPD - Postfix Policy Server:
    * Version: 5.0
    * Listen address: 127.0.0.1, port: 7777
    * SQL database account:
        - Database name: iredapd
        - Username: iredapd
        - Password: **************
    * Configuration file:
        - /opt/iredapd/settings.py
    * Related files:
        - /opt/iRedAPD-5.0
        - /opt/iredapd (symbol link to /opt/iRedAPD-5.0

iRedAdmin - official web-based admin panel:
    * Version: 1.3
    * Root directory: /opt/www/iRedAdmin-1.3
    * Config file: /opt/www/iRedAdmin-1.3/settings.py
    * Web access:
        - URL: https://mail.devops-alumno08.com/iredadmin/
        - Username: postmaster@devops-alumno08.com
        - Password: **************
    * SQL database:
        - Database name: iredadmin
        - Username: iredadmin
        - Password: **************

Roundcube webmail: /opt/www/roundcubemail-1.4.11
    * Config file: /opt/www/roundcubemail-1.4.11/config
    * Web access:
        - URL: http://mail.devops-alumno08.com/mail/ (will be redirected to https:// site)
        - URL: https://mail.devops-alumno08.com/mail/ (secure connection)
        - Username: postmaster@devops-alumno08.com
        - Password: **************
    * SQL database account:
        - Database name: roundcubemail
        - Username: roundcube
        - Password: **************
    * Cron job:
        - Command: "crontab -l -u root"

SOGo Groupware:
    * Web access: httpS://mail.devops-alumno08.com/SOGo/
    * Main config file: /etc/sogo/sogo.conf
    * Nginx template file: /etc/nginx/templates/sogo.tmpl
    * Database:
        - Database name: sogo
        - Database user: sogo
        - Database password: **************
    * SOGo sieve account (Warning: it's a Dovecot Master User):
        - file: /etc/sogo/sieve.cred
        - username: sogo_sieve_master@not-exist.com
        - password: **************
    * See also:
        - cron job of system user: sogo

netdata (monitor):
    - Config files:
        - All config files: /opt/netdata/etc/netdata
        - Main config file: /opt/netdata/etc/netdata/netdata.conf
        - Modified modular config files:
            - /opt/netdata/etc/netdata/go.d
            - /opt/netdata/etc/netdata/python.d
    - HTTP auth file (if you need a new account to access netdata, please
      update this file with command like 'htpasswd' or edit manually):
        - /etc/nginx/netdata.users
    - Log directory: /opt/netdata/var/log/netdata
    - SQL:
        - Username: netdata
        - Password: **************
        - NOTE: No database required by netdata.

```
## Alias
 
> La feature de alias de correo es de pago pero se puede utilizar sin pagar. Examina la base de datos, busca ayuda en google y averigua como crear un alias info@devops-master.com que apunte a las 2 cuentas creadas anteriormente.
> Cuando esté configurado, envia un correo a info y comprueba que ambos lo reciben.

Entramos en la bdd y configuramos un alias y dos forwardings:
```
use vmail;
INSERT INTO alias (address, domain, active) VALUES ('info@devops-alumno08.com','devops-alumno08.com',1);
INSERT INTO forwardings (address, forwarding, domain, dest_domain, is_list, active) VALUES ('info@devops-alumno08.com','postmaster@d
evops-alumno08.com','devops-alumno08.com','devops-alumno08.com',1,1);
INSERT INTO forwardings (address, forwarding, domain, dest_domain, is_list, active) VALUES ('info@devops-alumno08.com','enrique.sanz@d
evops-alumno08.com','devops-alumno08.com','devops-alumno08.com',1,1);
```
De esta forma todos los mails a info@devops-alumno08.com llegarán a postmaster y enrique.sanz

## Secondary MX.
> Enciende un servidor tipo CX11 con debian y mira como configurar este servidor como mx secundario.
> 
> Cuando lo tengas instalado y configurado intenta apagar el servidor principal. Cuando esté apagado envia un correo y mira en el log del secundario si lo recibe. Una vez recibido por el secundario enciende el primario y comprueba que el relay funciona.
