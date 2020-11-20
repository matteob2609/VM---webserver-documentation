# webserver
Breve documentazione per inizializzare un server web Ubuntu.

(Usare 'sudo' prima del comando se non si hanno i diritti necessari)

### 1. CONFIGURAZIONE IP & INSTALLAZIONE SSH / APACHE2

- _apt update_

- _apt upgrade_

- _nano /etc/hosts_ 

- _nano /etc/hostname_

`Checkpoint --> immettere il comando reboot per riavviare il server web e visualizzare l'hostname aggiornato.`

      reboot

- _apt install openssh-server_

- _nano /etc/netplan/00-installer-config.yaml_

      network:
        renderer: networkd
        ethernets:
          enp0s3:
            addresses: [172.16.29.105/16]
            gateway4: 172.16.1.7
            nameservers:
                addresses: [172.16.1.10, 1.1.1.1]
         version: 2

- _netplan try_

`Checkpoint --> verificare se l'indirizzo è stato modificato correttamente tramite il comando ip addr e verificare la connessione con il comando ping.`

      ip addr
      ping

- _apt install apache2_

`Checkpoint --> verificare l'installazione del server Apache aprendo il browser e mettendo nella barra degli indirizzi l'IP del server web, se viene visualizzata la pagina di default di Apache l'installazione è andata a buon fine.`

      172.16.29.105/index.html

---

### 2. CREAZIONE DI UN UTENTE PER UN SITO SPECIFICO

- _useradd -s /bin/bash -d /var/www/'nome_cartella_sito' -m 'nome_user'_

- _passwd 'password'_

---

### 3. AGGIUNTA DEL SITO WEB

- _cd /var/www/'nome_cartella_sito'_

- _mkdir log_

- _cd /etc/apache2/sites-available_

- _cp 000-default.conf 'nome_cartella_sito'.conf_

- _nano 'nome_cartella_sito'.conf_

`Togliere il commento nella riga ServerName e inserire il dominio del sito; In DocumentRoot inserire il percorso della cartella del sito; In ErrorLog e CustomLog togliere il $, le parentesi graffe e inserire il percorso della cartella del sito /log.`

---

### 4. ABILITAZIONE DEL SITO

- _a2ensite 'file_configurazione_sito'.conf_

- _systemctl restart apache2.service_

`Checkpoint --> immettere nella barra degli indirizzi del browser IP/nome_cartella_sito, se vengono visualizzati i file inseriti dall'utente registrato il sito funziona correttamente.`

---

### 5. ATTIVAZIONE DEL SERVIZIO FTP

- _apt install vsftpd_

- _nano /etc/vsftpd.conf_

      listen=yes

      listen_ipv6=NO

      anonymous_enable=NO

      local_enable=YES

      write_enable=YES

      local_umask=022

      dirmessage_enable=YES

      use_localtime=YES

      xferlog_enable=YES

      connect_from_port_20=YES

      xferlog_file=/var/log/vsftpd.log

      xferlog_std_format=YES

      ftpd_banner=Welcome to our  FTP service.

      chroot_local_user=YES

      local_root=/var/www/$USER

      user_sub_token=$USER

      allow_writeable_chroot=YES

      secure_chroot_dir=/var/run/vsftpd/empty

      pam_service_name=vsftpd

      rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem

      rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key

      ssl_enable=NO

      session_support=YES

      log_ftp_protocol=YES

- _systemctl start vsftpd_

- _systemctl enable vsftpd_
