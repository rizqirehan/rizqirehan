# Final Project Sistem Operasi Admin

 Rizqy M Raihan (22.83.0797)

# Latar Belakang
Kemajuan teknologi telah memudahkan kita dalam segala hal termasuk menyimpan data. Tidak perlu menggunakan penyimpanan fisik, ya, 
praktikum kali ini saya menginstall penyimpanan cloud, yaitu owncloud
berikut konfigurasi yang saya install


# Alat Yang Disiapkan
1. File ISO Ubuntu Server 22.04 LTS
2. VirtualBox

# Service Yang Diinstall
1. SSH
````
sudo apt install sshd
````
2. Cockpit
````
sudo apt install cockpit
````
# Konfigurasi OwnCloud
1. Create the occ helper script.
   
```
#!/bin/bash
 -u www-data php /var/www/owncloud/occ "$@"
````
```
sudo chmod +x occ
```

2. Install the required packages
````
sudo apt install apache2 php7.4 libapache2-mod-php7.4 php-mbstring php-xml php-mysql
````

3. Create a database for ownCloud.
````
sudo mysql -u root
CREATE DATABASE owncloud;
CREATE USER owncloud@localhost IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON owncloud.* TO owncloud@localhost;
FLUSH PRIVILEGES;
exit
````

4. Download the ownCloud package.
````
wget https://download.owncloud.org/server/stable/owncloud-latest.tar.gz
````

5. Extract the package and copy it to the web root directory.
````
tar -zxvf owncloud-latest.tar.gz
sudo mv owncloud /var/www/owncloud
````

6. Set the ownership of the ownCloud directory to the web server user.
````
sudo chown -R www-data:www-data /var/www/owncloud
````

7. setting apache2 untuk berjalan port 80
````
sudo nano /etc/apache2/sites-available/owncloud.conf
<VirtualHost *:80>
  ServerName your_domain_name
  DocumentRoot /var/www/owncloud
  <Directory /var/www/owncloud>
    Options FollowSymLinks MultiViews
    AllowOverride All
    Order allow,deny
    allow from all
  </Directory>
</VirtualHost>
sudo a2ensite owncloud.conf
sudo systemctl restart apache2
````

# Konfigurasi Firewall
1. Aktifkan Firewall
````
ufw enable
````

2. Ijinkan Port Berjalan Di Ubuntu
````
ufw allow 'apache2'
ufw allow 'ssh'
ufw allow 9090
````
# Block Acces Root for SSH
1. Open file /etc/ssh/sshd_config
````
permitlogin no
````

#**Deploy Ownlcloud Ke Publik**


**1. Buat Akun Cloudflare**
- Kunjungi [Cloudflare](https://www.cloudflare.com/) dan daftar/login ke akun Anda.  

**2. Tambahkan Domain ke Cloudflare**
1. **Masuk ke Dashboard Cloudflare**  
2. Klik **"Add a Site"**  
3. Masukkan nama domain Anda (misal: `example.com`)  
4. Pilih paket **Free** (atau paket lain jika dibutuhkan)  
5. Cloudflare akan menampilkan **nameserver baru** yang harus Anda gunakan  

---
**3. Update Nameserver di Registrar Domain**
- Masuk ke **pengaturan domain** di penyedia domain Anda (misalnya, Namecheap, GoDaddy, atau Niagahoster).  
- Ganti **nameserver lama** dengan **nameserver Cloudflare** yang diberikan.  
- Tunggu beberapa menit hingga perubahan diterapkan.  

**4. Upload Website ke Cloudflare Pages (Jika Tanpa Hosting)**
1. **Masuk ke Dashboard Cloudflare**  
2. Klik **"Pages"** > **"Create a Project"**  
3. Pilih metode:
   - **GitHub/GitLab Integration** → Hubungkan repository (misal: proyek React, Next.js, atau HTML)  
   - **Upload Manual** → Jika hanya HTML/CSS/JS statis, pilih **"Direct Upload"** dan unggah file `index.html`  
4. Konfigurasi build (jika pakai framework seperti Next.js atau Vue)  
5. Klik **"Deploy"**  

---

**5. Konfigurasi DNS (Jika Pakai Hosting Sendiri)**
Jika website di-host di server lain dan hanya ingin menggunakan Cloudflare sebagai CDN dan proteksi:
1. Masuk ke **"DNS"** di Cloudflare  
2. Tambahkan **A Record**:
   - **Name:** `@` (atau `www` untuk subdomain)  
   - **IPv4 Address:** IP server hosting Anda  
   - **Proxy Status:** **"Proxied"** (agar Cloudflare aktif)  
3. Simpan perubahan  

**6. Aktifkan SSL/TLS**
1. Masuk ke **SSL/TLS** di Cloudflare  
2. Pilih mode **"Full (Strict)"** jika hosting memiliki sertifikat SSL, atau **"Flexible"** jika tidak  
3. Website Anda sekarang menggunakan HTTPS dengan Cloudflare  

**7. Konfigurasi Firewall & Keamanan (Opsional)**
- **Security Level**: Atur ke **"High"** untuk proteksi ekstra dari serangan  
- **DDoS Protection**: Aktifkan jika perlu  
- **Page Rules**: Buat aturan khusus seperti redirect otomatis atau cache  

**8. Cek Website**
- Pastikan domain Anda sudah mengarah ke Cloudflare dengan mengecek menggunakan:  

**|------------------------|**
   **nslookup example.com**
**|------------------------|**  



