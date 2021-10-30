# 📖 Laporan Resmi Jaringan Komputer Modul 2 E16 2021 

**Anggota Kelompok:**
 - Ishaq Adheltyo (05111940000167)

## 🏷️ Soal 1: EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet 

### ✍️ Langkah-Langkah Pengerjaan:

#### 📡 Topologi
![image](https://user-images.githubusercontent.com/49280352/139528909-3ed4381a-78ea-46ac-bdec-17f7e866fa05.png)

#### 🔧 Edit Network Configuration Foosha

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.37.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.37.2.1
	netmask 255.255.255.0
```

#### 🔧 Edit Network Configuration Loguetown

```
auto eth0
iface eth0 inet static
	address 10.37.1.2
	netmask 255.255.255.0
	gateway 10.37.1.1
```

#### 🔧 Edit Network Configuration Alabasta

```
auto eth0
iface eth0 inet static
	address 10.37.1.3
	netmask 255.255.255.0
	gateway 10.37.1.1
```

#### 🔧 Edit Network Configuration EniesLobby

```
auto eth0
iface eth0 inet static
	address 10.37.2.2
	netmask 255.255.255.0
	gateway 10.37.2.1
```


#### 🔧 Edit Network Configuration Water7

```
auto eth0
iface eth0 inet static
	address 10.37.2.3
	netmask 255.255.255.0
	gateway 10.37.2.1
```


#### 🔧 Edit Network Configuration Skypie

```
auto eth0
iface eth0 inet static
	address 10.37.2.4
	netmask 255.255.255.0
	gateway 10.37.2.1
```
#### 🔧 Configurasi tambahan pada Foosha

- Tambahkan perintah dibawah pada .bashrc
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s [Prefix IP].0.0/16
```
Keterangan:
- iptables merupakan suatu tools dalam sistem operasi Linux yang berfungsi sebagai filter terhadap lalu lintas data.
- Nat adalah metode penafsiran alamat jaringan yang digunakan untuk menghubungkan lebih dari satu komputer ke jaringan internet dengan satu IP.
- Masquerade berfungsi untuk menyamarkan paket, misal mengganti alamat pengirim dengan alamat router.

- Buka /etc/resolv.conf

```
cat /etc/resolv.conf
```

Hasil dari file diatas, salin pada setiap node yang ada pada topologi agar node dapat tersambung dengan internet.

## 🏷️ Prepare Tools: Sebelum lanjut ke nomor berikutnya pastikan meginstall semua tools dibawah

### ✍️ Langkah-Langkah Pengerjaan:

#### 🖥️ Node Alabasta dan Loguetown

```
apt-get update
apt-get install nano
apt-get install dnsutils -y
apt-get install lynx -y
```

#### 🖥️ Node EniesLobby

```
apt-get update
apt-get install nano
apt-get install bind9 -y
```

#### 🖥️ Node Water7

```
apt-get update
apt-get install nano
apt-get install bind9 -y
```

#### 🖥️ Node Skypie

- Install tools yang dibutuhkan pada seluruh soal

```
apt-get update
apt-get install nano
apt-get install bind9 -y
apt-get install apache2 -y
apt-get install php -y
apt-get install libapache2-mod-php7.0
```

- Download isi website yang sudah disediakan pada soal dan simpan di root agar tidak hilang (**cukup satu kali dijalankan**)

```
apt-get install wget -y
apt-get install unzip -y
wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/archive/refs/heads/main.zip
unzip main.zip
cd Praktikum-Modul-2-Jarkom-main/
unzip franky.zip
unzip general.mecha.franky.zip
unzip super.franky.zip
```

## 🏷️ Soal 2: Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku 

### ✍️ Langkah-Langkah Pengerjaan:

#### 🖥️ Node EniesLobby

- Edit Config local dengan menaruh franky.yyy.com dengan type master dan directory bind data dalam folder kaizoku/franky.yyy.com

```
nano /etc/bind/named.conf.local
```

```
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "franky.e16.com" {
        type master;
        file "/etc/bind/kaizoku/franky.e16.com";
};
```

- Buat bind data file pada folder kaizoku dengan menaruh franky.yyy.com serta aliasnya

```
mkdir /etc/bind/kaizoku
cp /etc/bind/db.local /etc/bind/kaizoku/franky.e16.com
nano /etc/bind/kaizoku/franky.e16.com
```

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.e16.com. root.franky.e16.com. (
                        2021102501      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.e16.com.
@       IN      A       10.37.2.2 ;IP EniesLobby
www     IN      CNAME   franky.e16.com.
```

```
service bind9 restart
```

### 👨‍💻 Testing:

#### 🖥️ Node Loguetown atau Alabasta

- Ubah resolve.conf dengan menambahkan ip enieslobby

```
nano /etc/resolv.conf
```

```
nameserver 10.37.2.2
```

- Test franky.e16.com dan www.franky.e16.com menggunakan ping

```
ping franky.e16.com
ping www.franky.e16.com
```

![image](https://user-images.githubusercontent.com/49280352/139533320-ec139c22-1ba8-45dd-b4ec-88995dd878be.png)

Jika hasil seperti gambar diatas maka sudah berhasil.


## 🏷️ Soal 3: Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie

### ✍️ Langkah-Langkah Pengerjaan:

#### 🖥️ Node EniesLobby

- Edit bind data file pada folder kaizoku dengan menaruh super.franky.yyy.com serta aliasnya

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.e16.com. root.franky.e16.com. (
                        2021102501      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.e16.com.
@       IN      A       10.37.2.2 ;IP EniesLobby
www     IN      CNAME   franky.e16.com.
super   IN      A       10.37.2.4 ;IP Skypie
www.super       IN      CNAME   super.franky.e16.com.
```

```
service bind9 restart
```

### 👨‍💻 Testing:

#### 🖥️ Node Loguetown atau Alabasta

- Test franky.e16.com dan www.franky.e16.com menggunakan ping

```
ping super.franky.e16.com
ping www.super.franky.e16.com
```

![image](https://user-images.githubusercontent.com/49280352/139533499-fc159676-ebc6-4552-b3d5-422d6091b2c6.png)

Jika hasil seperti gambar diatas maka sudah berhasil.

## 🏷️ Soal 4: Buat juga reverse domain untuk domain utama

### ✍️ Langkah-Langkah Pengerjaan:

#### 🖥️ Node EniesLobby

- Edit Config local dengan menaruh 2.37.10.in-addr.arpa dengan type master dan directory bind data dalam folder kaizoku/2.37.10.in-addr.arpa

```
nano /etc/bind/named.conf.local
```

```
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "franky.e16.com" {
        type master;
        file "/etc/bind/kaizoku/franky.e16.com";
};
zone "2.37.10.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.37.10.in-addr.arpa";
};
```

- Buat file bind data untuk 2.37.10.in-addr.arpa dan arahkan ke PTR ke 2 / IP EniesLobby

```
cp /etc/bind/db.local /etc/bind/kaizoku/2.37.10.in-addr.arpa
nano /etc/bind/kaizoku/2.37.10.in-addr.arpa
```

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.e16.com. root.franky.e16.com. (
                        2021102501      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.37.10.in-addr.arpa.   IN      NS      franky.e16.com.
2       IN      PTR     franky.e16.com.
```

```
service bind9 restart
```

### 👨‍💻 Testing:

#### 🖥️ Node Loguetown atau Alabasta

- Test reverse dengan host -t PTR mengarah ke IP EniesLobby

```
host -t PTR 10.37.2.2
```

![image](https://user-images.githubusercontent.com/49280352/139533649-6272ce9f-722b-4623-83bd-3c60d0c76081.png)

Jika hasil seperti gambar diatas maka sudah berhasil.

## 🏷️ Soal 5: Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama

### ✍️ Langkah-Langkah Pengerjaan:

#### 🖥️ Node EniesLobby

- Edit Config local dengan menambah pada zone franky.yyy.com dengan enable notify, also-notify, allow-transfer untuk memberikan akses kepada DNS Slave (Water7)

```
nano /etc/bind/named.conf.local
```

```
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "franky.e16.com" {
    type master;
    notify yes;
    also-notify { 10.37.2.3; }; // Masukan IP Water7 tanpa tanda petik
    allow-transfer { 10.37.2.3; }; // Masukan IP Water7 tanpa tanda petik
    file "/etc/bind/kaizoku/franky.e16.com";
};
zone "2.37.10.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.37.10.in-addr.arpa";
};
```

```
service bind9 restart
```

#### 🖥️ Node Water7

- Edit Config local dengan menambah zone franky.yyy.com dengan enable type slave, master berisi IP DNS asalnya / masternya, dan file config dalam bind

```
nano /etc/bind/named.conf.local
```

```
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "franky.e16.com" {
    type slave;
    masters { 10.37.2.2; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/franky.e16.com";
};
```

```
service bind9 restart
```

### 👨‍💻 Testing:

#### 🖥️ Node Loguetown atau Alabasta

- Ubah resolve.conf dengan menambahkan ip enieslobby dan water7

```
nano /etc/resolv.conf
```

```
nameserver 10.37.2.2
nameserver 10.37.2.3
```

- Test franky.yyy.com dan www.franky.yyy.com  dengan kondisi bind9 mati pada EniesLobby dengan command ```service bind9 stop``` pada EniesLobby

```
ping franky.e16.com
ping www.franky.e16.com
```

![image](https://user-images.githubusercontent.com/49280352/139533929-e1bbbb6f-44e3-4928-ae37-b2cc004d6fd0.png)

Jika hasil seperti gambar diatas maka sudah berhasil, **jangan lupa nyalain bind9 di node EniesLobby nya lagi ya gais**.

## 🏷️ Soal 6: Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo

### ✍️ Langkah-Langkah Pengerjaan:

#### 🖥️ Node EniesLobby

- Edit data bind franky.yyy.com dengan menambahkan nameserver baru mengarah IP Water7 dengan domain mecha

```
nano /etc/bind/kaizoku/franky.e16.com
```

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.e16.com. root.franky.e16.com. (
                        2021102501      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.e16.com.
@       IN      A       10.37.2.2 ;IP EniesLobby
www     IN      CNAME   franky.e16.com.
super   IN      A       10.37.2.4 ;IP Skypie
www.super       IN      CNAME   super.franky.e16.com.
ns1     IN      A       10.37.2.3 ;IP Water7
mecha   IN      NS      ns1
```

- Edit file option bind dengan comment  //dnssec-validation auto; dan menambahkan allow-query{any;}; untuk mengizinkan server lain melakukan query pada bind EniesLobby 

```
nano /etc/bind/named.conf.options
```

```
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0's placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //=====================================================================$
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
	//=====================================================================$
	//dnssec-validation auto;
	allow-query{any;};

	auth-nxdomain no;    # conform to RFC1035
	listen-on-v6 { any; };
	};
```

```
service bind9 restart
```

#### 🖥️ Node Water7

- Edit file option bind dengan comment  //dnssec-validation auto; dan menambahkan allow-query{any;}; untuk mengizinkan server lain melakukan query pada bind Water7 

```
nano /etc/bind/named.conf.options
```

```
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0's placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //=====================================================================$
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
	//=====================================================================$
	//dnssec-validation auto;
	allow-query{any;};

	auth-nxdomain no;    # conform to RFC1035
	listen-on-v6 { any; };
	};
```

- Edit config bind local dengan menambahkan zone mecha.franky.e16.com dengan type master dan bind data di /etc/bind/sunnygo/mecha.franky.e16.com

```
nano /etc/bind/named.conf.local
```

```
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "franky.e16.com" {
    type slave;
    masters { 10.37.2.2; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/franky.e16.com";
};
zone "mecha.franky.e16.com" {
        type master;
        file "/etc/bind/sunnygo/mecha.franky.e16.com";
};
```

- Buat file bind data di folder sunnygo dengan berisi mecha.franky.yyy.com dan alias www.mecha.franky.yyy.com

```
mkdir /etc/bind/sunnygo
cp /etc/bind/db.local /etc/bind/sunnygo/mecha.franky.e16.com
nano /etc/bind/sunnygo/mecha.franky.e16.com
```

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     mecha.franky.e16.com. root.mecha.franky.e16.com. (
                        2021102501      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      mecha.franky.e16.com.
@       IN      A       10.37.2.4 ;IP Skypie
www     IN      CNAME   mecha.franky.e16.com.
```

```
service bind9 restart
```

### 👨‍💻 Testing:

#### 🖥️ Node Loguetown atau Alabasta

- Test mecha.franky.yyy.com dan alias www.mecha.franky.e16.com dengan menggunakan ping

```
ping mecha.franky.e16.com
ping www.mecha.franky.e16.com
```

![image](https://user-images.githubusercontent.com/49280352/139534453-08857997-4563-4e28-b171-42dbce2950de.png)

Jika hasil seperti gambar diatas maka sudah berhasil.


## 🏷️ Soal 7: Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie

### ✍️ Langkah-Langkah Pengerjaan:

#### 🖥️ Node Water7

- Edit bind data mecha.franky.e16.com dengan menambahkan subdomain general.mecha.franky.yyy.com dan www.general.mecha.franky.yyy.com

```
nano /etc/bind/sunnygo/mecha.franky.e16.com
```

```

;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     mecha.franky.e16.com. root.mecha.franky.e16.com. (
                        2021102501      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      mecha.franky.e16.com.
@       IN      A       10.37.2.4 ;IP Skypie
www       IN      CNAME   mecha.franky.e16.com.
general IN      A       10.37.2.4 ;IP Skypie
www.general     IN      CNAME   general.mecha.franky.e16.com.
```

```
service bind9 restart
```

### 👨‍💻 Testing:

#### 🖥️ Node Loguetown atau Alabasta

- Test general.mecha.franky.yyy.com dan alias www.general.mecha.franky.e16.com dengan menggunakan ping

```
ping general.mecha.franky.e16.com
ping www.general.mecha.franky.e16.com
```

![image](https://user-images.githubusercontent.com/49280352/139535560-78deefaa-9b51-4024-8bdc-7473ddaabe9a.png)

Jika hasil seperti gambar diatas maka sudah berhasil.

## 🏷️ Soal 8: Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com.

### ✍️ Langkah-Langkah Pengerjaan:

#### 🖥️ Node EniesLobby

- Edit bind data franky.e16.com dengan mengubah ip tujuan dari EnniesLobby ke Skypie

```
nano /etc/bind/kaizoku/franky.e16.com
```

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.e16.com. root.franky.e16.com. (
                        2021102501      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.e16.com.
@       IN      A       10.37.2.4 ;IP Skypie 
www     IN      CNAME   franky.e16.com.
super   IN      A       10.37.2.4 ;IP Skypie
www.super       IN      CNAME   super.franky.e16.com.
ns1     IN      A       10.37.2.3 ;IP Water7
mecha   IN      NS      ns1
```

```
service bind9 restart
```

#### 🖥️ Node Skypie

- Jalankan Apache2

```
service apache2 start
```

- Buat config webserver untuk franky.e16.com pada sites-available

```
cd /etc/apache2/sites-available
cp 000-default.conf www.franky.e16.com.conf
nano /etc/apache2/sites-available/www.franky.e16.com.conf
```

```
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port t$
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/franky.e16.com
				ServerName franky.e16.com
        ServerAlias www.franky.e16.com

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn
				ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

- Enable config dengan a2ensite dan copy isi website dari root yang telah diekstrak pada awal ke dalam /var/www/franky.e16.com

```
cd /etc/apache2/sites-available/
a2ensite www.franky.e16.com.conf
service apache2 restart
cd
cd Praktikum-Modul-2-Jarkom-main
cp -r franky /var/www/franky.e16.com
```

### 👨‍💻 Testing:

#### 🖥️ Node Loguetown atau Alabasta

- Buka website franky.yyy.com atau www.franky.yyy,com dengan lynx

```
lynx http://franky.e16.com
lynx http://www.franky.e16.com
```

![image](https://user-images.githubusercontent.com/49280352/139535911-e61c110b-f3cf-4755-98fc-1daf61b14f62.png)

Jika hasil seperti gambar diatas maka sudah berhasil.

## 🏷️ Soal 9: Setelah itu, Luffy juga membutuhkan agar url www.franky.yyy.com/index.php/home dapat menjadi menjadi www.franky.yyy.com/home

### ✍️ Langkah-Langkah Pengerjaan:

#### 🖥️ Node Skypie

- Edit www.franky.e16.com.conf dengan menambahkan alias directory /var/www/franky.e16.com/index.php/home menjadi /home

```
nano /etc/apache2/sites-available/www.franky.e16.com.conf
```

```
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port t$
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/franky.e16.com
				ServerName franky.e16.com
        ServerAlias www.franky.e16.com

		    Alias "/home" "/var/www/franky.e16.com/index.php/home"

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn
				ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

```
service apache2 restart
```

### 👨‍💻 Testing:

#### 🖥️ Node Loguetown atau Alabasta

- Buka website franky.yyy.com/home atau www.franky.yyy,com/home dengan lynx

```
lynx http://franky.e16.com/home
lynx http://www.franky.e16.com/home
```

![image](https://user-images.githubusercontent.com/49280352/139535911-e61c110b-f3cf-4755-98fc-1daf61b14f62.png)

Jika hasil seperti gambar diatas maka sudah berhasil.

## 🏷️ Soal 10: Setelah itu, pada subdomain www.super.franky.yyy.com, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/super.franky.yyy.com

### ✍️ Langkah-Langkah Pengerjaan:

#### 🖥️ Node Skypie

- Buat config webserver baru untuk super.franky.e16.com pada sites-available apache

```
cd /etc/apache2/sites-available
cp 000-default.conf www.super.franky.e16.com.conf
nano /etc/apache2/sites-available/www.super.franky.e16.com.conf
```

```
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port t$
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/super.franky.e16.com
        ServerName super.franky.e16.com
        ServerAlias www.super.franky.e16.com

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
				#LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
				#Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

- Enable config dengan a2ensite dan copy isi website super.franky dari requirement pada soal yang sudah diekstrak pada awal

```
a2ensite www.super.franky.e16.com.conf
service apache2 restart
cd
cd Praktikum-Modul-2-Jarkom-main
cp -r super.franky /var/www/super.franky.e16.com
```

### 👨‍💻 Testing:

#### 🖥️ Node Loguetown atau Alabasta

- Buka website super.franky.e16.comatau www.super.franky.e16.com dengan lynx

```
lynx http://super.franky.e16.com
lynx http://www.super.franky.e16.com
```

![image](https://user-images.githubusercontent.com/49280352/139536225-30f4eb34-9420-449e-ba6e-3e3830e67e85.png)

Jika hasil seperti gambar diatas maka sudah berhasil.

## 🏷️ Soal 11: Akan tetapi, pada folder /public, Luffy ingin hanya dapat melakukan directory listing saja.

### ✍️ Langkah-Langkah Pengerjaan:

#### 🖥️ Node Skypie

- Edit config super.franky.e16.com di sites-available dengan menambah beberapa rules untuk directory listing menggunakan Option -Indexes untuk disable explore folder dibawah public

```
nano /etc/apache2/sites-available/www.super.franky.e16.com.conf
```

```
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port t$
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/super.franky.e16.com
        ServerName super.franky.e16.com
        ServerAlias www.super.franky.e16.com

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
	#LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

	<Directory /var/www/super.franky.e16.com>
                Options +Indexes
        </Directory>
	
	<Directory /var/www/super.franky.d09.com/public/*>
        	Options -Indexes
	</Directory>

        <Directory /var/www/super.franky.e16.com/public>
                Options +Indexes
        </Directory>

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
	#Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

```
service apache2 restart
```

### 👨‍💻 Testing:

#### 🖥️ Node Loguetown atau Alabasta

- Buka website super.franky.C13.com/public.comatau www.super.franky.C13.com/public dengan lynx

```
lynx http://super.franky.e16.com/public
lynx http://www.super.franky.e16.com/public
```

- Untuk hasil /public

![image](https://user-images.githubusercontent.com/49280352/139536507-2c4696e4-a779-4d2f-ba41-c4447e478d6d.png)

- Ketika membuka folder /public/*

![image](https://user-images.githubusercontent.com/49280352/139536566-5b90851b-bd28-446f-8df2-1ee7ce26af1b.png)

Jika hasil seperti gambar diatas maka sudah berhasil.
