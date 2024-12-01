# Final Project OS Server dan Sistem Admin 23.83.1018
Dalam Repository ini, saya akan mengimplementasikan 5 layanan server yang terdiri dari SSH, DHCP, FTP dan Samba, DNS dan Mail Server.

## Layanan Server
<a href="#ssh">1. Layanan Server SSH</a> <br>
<a href="#dhcp">2. Layanan Server DHCP</a> <br>
<a href="#">3. Layanan Server FTP dan Samba</a> <br>
<a href="#">4. Layanan Server DNS</a> <br>
<a href="#">5. Layanan Server Mail Server</a> <br>

<h2 id="ssh">Instalasi Layanan Server SSH</h2>

### 1.1 Instalasi SSH
1. Lakukan instalasi paket SSH dengan mengetik perintah
```
sudo apt-get install
```
### 1.2 Konfigurasi SSH
1. Ketik perintah untuk mengedit konfigurasi
```
nano /etc/ssh/sshd_config
```
2. Edit sesuai dengan konfigurasi berikut
```
Include /etc/ssh/sshd_config.d/*.conf

Port 22
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::

#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key

# Ciphers and keying
#RekeyLimit default none

# Logging
#SyslogFacility AUTH
#LogLevel INFO

# Authentication:

#LoginGraceTime 2m
PermitRootLogin yes
#StrictModes yes
#MaxAuthTries 6
#MaxSessions 10

#PubkeyAuthentication yes

# Expect .ssh/authorized_keys2 to be disregarded by default in future.
#AuthorizedKeysFile     .ssh/authorized_keys .ssh/authorized_keys2

#AuthorizedPrincipalsFile none

#AuthorizedKeysCommand none
#AuthorizedKeysCommandUser nobody
```
"PermitRootLogin" bisa di biarkan "prohibit-password" jika anda tidak mengizikan root untuk login dengan remote SSH. <br> <br>
3. Restart SSH
```
sudo systemctl restart sshd
```
<br>
4. Tes Konfigurasi yang telah dilakukan.

```
ssh root@hasbi -p 22
```

<h2 id="dhcp">Instalasi Layanan Server DHCP</h2>
