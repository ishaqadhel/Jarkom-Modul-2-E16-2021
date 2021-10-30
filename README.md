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
