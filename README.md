# Building Minecraft Server - Final Project OS Server dan Sistem Admin 23.83.1018
Untuk spesifikasi server yang saya gunakan, saya memberikan alokasi RAM sebesar 4GB dengan storage 50GB, serta prosesor yang diberikan sebesar 4 core. <br> <br>
![image](https://github.com/user-attachments/assets/ba9bcdb1-c5c3-4dcf-8341-4d5d5d1cf85e)

Dalam Repository ini, saya akan mengimplementasikan beberapa layanan server yang terdiri dari :

## Layanan Server
<a href="#ssh">1. SSH</a> <br>
<a href="#jdk">2. Java Development Kit (JDK) </a> <br>
<a href="#ufw">3. UFW/Firewall</a> <br>
<a href="#rsy">4. Rsync + cron</a> <br>
<a href="#mon">5. Netdata</a> <br>
<a href="#apache">6. Apache</a> <br>
<a href="#test">Uji Coba Server</a> <br>

<h2 id="ssh">1. Instalasi dan Konfigurasi SSH</h2>

### Instalasi SSH
1. Lakukan instalasi paket SSH dengan mengetik perintah
```
sudo apt-get install openssh-server
```
![image](https://github.com/user-attachments/assets/51108d19-d5f1-4186-8153-5e6e5bf4a2fb)

2. Jalankan SSH 
```
sudo systemctl start ssh
```
![image](https://github.com/user-attachments/assets/9ebdf98a-f463-4dd9-b3d3-70a275b5140e)

3. Cek status SSH untuk memastikan bahwa SSH telah berjalan dan dapat di remote
```
sudo systemctl status ssh
```
![image](https://github.com/user-attachments/assets/cca967a2-262c-4184-a293-0e164ff99453)

4. Konfigurasi SSH
```
sudo nano /etc/ssh/sshd_config
```
Ubah konfigurasi seperti pada teks dibawah
```
Port 2222
PermitRootLogin no
PasswordAuthentication no
```
![image](https://github.com/user-attachments/assets/942b010c-39d3-4864-97e6-8156c7c97b98)

5. Restart SSH
```
sudo systemctl restart ssh
```
![image](https://github.com/user-attachments/assets/c3952d44-bb45-46b3-bbd7-a0e5bd8231cb)


<h2 id="jdk">2. Instalasi dan Konfigurasi Java Development Kit</h2>

1. Tambahkan repository untuk JDK
```
sudo add-apt-repository ppa:openjdk-r/ppa
```
![image](https://github.com/user-attachments/assets/dc107a22-2561-4af8-99d8-2448b5d02dea)

2. Install JDK
```
sudo apt install openjdk-17-jre-headless wget screen ufw -y
```
![image](https://github.com/user-attachments/assets/9ad1b6f4-c0ae-4c85-87e4-673c85cf6180)

3. Pastikan Java telah terinstall
```
java -version
```
![image](https://github.com/user-attachments/assets/c717d784-dca6-4ee6-9b45-c33e4a5afc73)

4. Buat direktori untuk server Minecraft
```
mkdir ~/minecraft && cd ~/minecraft
```
![image](https://github.com/user-attachments/assets/ad06f265-8a3b-4a81-8f7b-4b4ef216a3d5)

5. Download Server Minecraft
```
wget https://launcher.mojang.com/v1/objects/c8f83c5655308435b3dcf03c06d9fe8740a77469/server.jar
```
![image](https://github.com/user-attachments/assets/d64540a0-dae1-436b-b0ef-83b948bb03a3)

6. Edit file eula.txt
```
nano eula.txt
```
lalu ubah konfigurasinya
```
eula=true
```
![image](https://github.com/user-attachments/assets/dad7a643-d86d-461d-a415-ee323d6d24d7)

7. Install screen agar server dapat berjalan di Background
```
sudo apt install screen
```
![image](https://github.com/user-attachments/assets/c06f62ec-464a-4ac8-bafe-374e3183e92b)

8. Jalankan Server untuk Pertama kalinya (Untuk tahapan ini, disarankan untuk dijalankan setelah semua langkah-langkah sudah terpenuhi)
```
screen -S minecraft
java -Xmx1024M -Xms1024M -jar server.jar nogui
```
![image](https://github.com/user-attachments/assets/1962e8d7-d6d6-497e-89db-a9d419a15591)

<h2 id="ufw">3. Konfigurasi UFW/Firewall</h2>

1. Izinkan port SSH yang baru
```
sudo ufw allow 2222/tcp
```
![image](https://github.com/user-attachments/assets/dcc3c939-ccb2-4862-887f-29b3219bbb9c)

2. Izinkan juga port default untuk Server Minecraft
```
sudo ufw allow 25565/tcp
```
![image](https://github.com/user-attachments/assets/22eaf604-18a6-49fb-94fb-369b04f97f17)

3. Aktifkan Firewall
```
sudo ufw enable
```
![image](https://github.com/user-attachments/assets/430d0519-67bf-4837-a43a-1ed9eb0c4c47)

4. Cek status dari firewall
```
sudo ufw status
```
![image](https://github.com/user-attachments/assets/20df1faa-e3ae-47e9-8eb1-402cdcb03ad3)

<h2 id="rsy">4. Layanan Backup dengan Rsync + cron</h2>

1. Install rsync
```
sudo apt install rsync -y
```
![image](https://github.com/user-attachments/assets/baba688c-5fb9-44a4-9535-b66eaca6aae7)

2. Buat skrip untuk backup server dengan masuk ke:
```
nano ~/minecraft-backup.sh
```
lalu isi dengan:
```
#!/bin/bash
BACKUP_DIR="/path/to/backup"
MINECRAFT_DIR="/home/youruser/minecraft"

rsync -av --delete $MINECRAFT_DIR $BACKUP_DIR
```
![image](https://github.com/user-attachments/assets/a32d1a2a-a292-4930-a3f2-9e18fa2770f6)

3. Beri izin untuk mengeksekusi
```
chmod +x ~/minecraft-backup.sh
```
![image](https://github.com/user-attachments/assets/fbdd6551-5382-4c4d-bccb-15f0ea585410)

4. Tambahkan ke cron untuk backup otomatis
```
crontab -e
```
Lalu pilih nomor 1 dan tambahkan:
```
0 2 * * * /home/<username kamu>/minecraft-backup.sh
```
![image](https://github.com/user-attachments/assets/ec6ce5ac-9822-4208-ad65-b66c2a277ce2)

<h2 id="mon">5. Monitoring dengan Netdata</h2>

1. Install Netdata
```
sudo apt install netdata -y
```
![image](https://github.com/user-attachments/assets/34888fc8-2760-48d0-8c46-2b7fb9a2b773)

2. Izinkan Firewall untuk tcp default netdata
```
sudo ufw allow 19999/tcp
```
![image](https://github.com/user-attachments/assets/71377b37-a311-4253-91e6-054ffbb12222)

3. Restart UFW dan cek statusnya
```
sudo ufw reload && sudo ufw status
```
![image](https://github.com/user-attachments/assets/af36c02a-f291-4e61-9a13-09aa03b02d91)

4. Cek alamat ip dengan port yang di listen oleh netdata
```
ss -antpl | grep 19999
```
![image](https://github.com/user-attachments/assets/390ce378-c096-4a77-b94c-7c2fa7ae5888)

5. Jika alamat ip tidak sesuai dengan server, ubah konfigurasinya dengan masuk ke:
```
nano /etc/netdata/netdata.conf
```
lalu ubah alamat ip pada bind socket sesuai dengan alamat ip server kamu. <br>
![image](https://github.com/user-attachments/assets/eb4e34e4-4c09-4b53-9061-a451f830938c)

6. Restart netdata
```
systemctl restart netdata
```
![image](https://github.com/user-attachments/assets/7c371ebd-9e2c-4e34-ab22-a1226d278a09)

7. Akses `<alamat ip kamu>:19999` pada web browser. <br>
![image](https://github.com/user-attachments/assets/8f017c6a-c5f8-4236-aad1-dab3d74d1998)

<h2 id="apache">6. Layanan Web Server Apache</h2>

1. Install APACHE terlebih dahulu
```
sudo apt install apache2 -y
```
![image](https://github.com/user-attachments/assets/5d6e2aeb-5cfb-43b5-ad92-85023c7370c6)

2. Pastikan apache telah aktif dengan mengetik perintah:
```
sudo systemctl status apache
```
![image](https://github.com/user-attachments/assets/b7fedaab-fb16-4309-818d-7bcf07ed562c)

3. Berikan izin firewall kepada Apache agar dapat diakses
```
sudo ufw allow 'Apache'
sudo ufw enable
sudo ufw status
```
![image](https://github.com/user-attachments/assets/e1422603-2117-4126-b7da-3d0484dc5bb0)

4. Buka alamat ip kamu di web browser dan pastikan apache dapat diakses. <br>
![image](https://github.com/user-attachments/assets/166b514a-98c6-40cb-ad8c-b7297a0d9927) <br>
Untuk mengedit isi web-nya, kamu bisa masuk ke
```
sudo nano /var/www/html/index.html
```
Untuk mengisi web-nya, bisa disesuaikan dengan preferensi kamu sendiri

<h2 id="test">UJI COBA SERVER</h2>

1. Masukkan IP Address Server ke dalam Game Minecraft. <br> <br>
![IP Address](https://github.com/user-attachments/assets/77115da3-0f31-421d-bce3-2b9476ef9b0e)

2. Player Berhasil bergabung ke Server. <br> <br>
![Player Berhasil Bergabung](https://github.com/user-attachments/assets/7bcbba02-bcf9-4f87-8332-bf6e54ffc6dd)

**JIKA ADA ERROR FAILED TO VERIFY USERNAME!** Kamu harus masuk kedalam file konfigurasi servernya:
```
nano ~/minecraft/server.properties
```
lalu ubah baris `online-mode=True` menjadi `online-mode=false`. 

