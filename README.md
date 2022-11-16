# Jarkom Modul 3 B02
```
Muhammad Daffa Aldriantama (5025201177)
Burhanudin Rifa (5025201191)
Maisan Auliya (5025201137)
```

## 1.  no 1 dan 2
Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server (1), dan Ostania sebagai DHCP Relay (2).

Kelompok kami membuat topologi sebagai berikut :
![Alt text](/img/[no 1].png)


Lalu melakukan network configuration pada masing-masing node

- Ostania
```auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.4.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.4.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.4.3.1
	netmask 255.255.255.0
```
- WISE 
```
auto eth0
iface eth0 inet static
	address 10.4.2.2
	netmask 255.255.255.0
	gateway 10.4.2.1
```
- Berlint
```
auto eth0
iface eth0 inet static
	address 10.4.2.3
	netmask 255.255.255.0
	gateway 10.4.2.1

```
- Westalis
```
auto eth0
iface eth0 inet static
	address 10.4.2.4
	netmask 255.255.255.0
	gateway 10.4.2.1

```
- SSS, Garden, Eden, NewstonCastle, Kemono Park
```
auto eth0
iface eth0 inet dhcp

```

Kemudian pada ostania melakukan seperti berikut:
<gambar 1.2>
Dimana nanti yang bagian No 2 (dari baris 8) akan di edit konfigurasinya pada /etc/default/isc-dhcp-relay .

## 2. No 3-4
Semua client yang ada harus menggunakan konfigurasi IP dari DHCP server. Client melalui Switch 1 mendapatkan range IP dari 10.4.1.50 – 10.4.1.88 dan 10.4.1.120 – 10.4.1.155 

Pada westalis dimasukan script sebagai berikut :
<gambar 2>
- Seperti biasa kita lakukan konfigurasi pada westalis terlebih dahulu (pada baris 21-23)
- Untuk soal no 3, diminta untuk melakukan set IP (baris 33-43)
- Untuk soal no 4, client melalui switch 3 mendapatkan range IP 10.4.3.10 - 10.4.3.30 dan 10.4.3.60 - 10.4.3.85 (baris 45-52) lalu semua nya disimpan di /etc/dhcp/dhcpd.conf

## 3. No 5
Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut
Sebelum melakukan konfigurasi pada WISE kita perlu melakukan hal berikut terlebih dahulu
```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
  apt-get update
  apt-get install bind9 -y
  service bind9 start

```
Kemudian pada WISE kita melakukan konfigurasi sebagai berikut
```
 echo "
  options {
        directory \"/var/cache/bind\";
        
        forwarders {
              192.168.122.1;
        };
        
        // dnssec-validation auto;
        allow-query { any; };
        auth-nxdomain no;    # conform to RFC135
        listen-on-v6 { any; };
  };
  " > /etc/bind/named.conf.options
  service bind9 restart

```

## 4. no 6
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit.

Pada Westalis dilakukan script sebagai berikut
<gambar 4>
## 5. no 7
Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP 192.172.3.13

Menambahkan konfigurasi berikut pada Westalis
<gambar 5>
Dan pada interface WISE sebagau berikut
<gambar 5.2>

## Proxy Server
Pada Proxy Server di Berlint, Loid berencana untuk mengatur bagaimana Client dapat mengakses internet. Artinya setiap client harus menggunakan Berlint sebagai HTTP & HTTPS proxy. Adapun kriteria pengaturannya adalah sebagai berikut:

1. Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh)
2. Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan)
3. Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com)
4. Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)
5. Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur

Untuk memenuhi kriteria kriteria diatas dilakukan konfigurasi sebagai berikut: Pada Berlint
<gambar 6>
Setelah dilakukan konfigurasi diatas dilakukan restart pada squid dengan service squid restart pada WISE
<gambar 6.2>
Setelah dilakukan konfigurasi diatas dilakukan restart pada bind dengan 
```service bind9 restart```
