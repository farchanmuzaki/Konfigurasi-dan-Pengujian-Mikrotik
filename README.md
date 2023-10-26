# Konfigurasi-dan-Pengujian-Mikrotik
Ini adalah page untuk belajar konfigurasi dasar dan pengujian

Pada Router kita diminta mengkonfigurasi NTP, Web Proxy, Hotspot dengan RADIUS, DHCP Server, dan firewall. Untuk server RADIUS, kita akan menggunakan userman yang terinstal pada Router. Sedangkan untuk mengkonfigurasi mikrotik kita akan mengguanakan software winbox yang bisa didownload melalui Website MikroTik.

Oke, untuk mempermudah kita soal paket 4 ini menjadi beberapa task.

Konfigurasi IP Address, NTP dan NAT Masquerade pada Router
Konfigurasi Web Proxy
Konfigurasi DHCP Server untuk LAN dan WLAN
Konfigurasi Hotspot pada Router (ether3)
Konfigurasi RADIUS Userman pada Router
Opsi 1: Konfigurasi Access Point Mikrotik RouterOS
Opsi 2: Konfigurasi Access Point TPLink
Konfigurasi Firewall
Seperti biasa, pastikan mikrotik telah direset ke no default konfiguration. Selain itu agar kita tidak bingung mana mikrotik yang dijadikan router, mana yang dijadikan accesspoint (jika menggunakan mikrotik). Maka, kita konfigurasi terlebih dahulu identitynya.
Pada winbox mikrotik, buka System → identity. Set Identity sesuai yang kamu inginkan. Di sini saya set "Router" untuk mikrotik yang dijadikan router dan "AP" untuk mikrotik yang dijadikan Access Point.


A. Konfigurasi IP Address, NTP dan NAT Masquerade pada Router
Kita mulai dengan request dhcp client pada ether1.
Buka menu IP → DHCP Client, tambah baru dengan klik icon "+". Pilih interfacenya ether1.


Sehingga ether1 mendapat IP secara otomatis.

Selanjutnya adalah konfigurasi IP address untuk ether2 (LAN) dan ether3 (WLAN), ketentuanya:
ether2 = 192.168.100.1/25
ether3 = 192.168.200.1/24
Caranya buka menu IP → Addresses, tambah baru dengan klik icon "+". Masukan ip address dan interface sesuai ketentuan di soal.


Sekarang ether1-3 sudah dikonfigurasi alamat IP.

Berikutnya coba cek koneksi dari mikrotik ke internet dengan ping. Buka menu New Terminal pada winbox mikrotik.


Jika masih gagal, cek default gateway (buka IP → Route) dan DNS server (buka IP → DNS).

Jika Router sudah terhubung ke internet, selanjutnya agar client bisa terhubung ke internet juga nantinya, kita perlu setting NAT Masquarede.
Buka IP → Firewall → NAT, tambah rule baru dengan parameter yang diisi sebagai berikut.
Chain: scrcnat
Out. Inteface: ether1 (interface yang terhubung ke arah internet)
Action: masquerade




Selain itu kita juga diminta konfiguras NTP (Network Time Protocol) untuk sinkronisasi waktu. Caranya buka menu System → SNTP Client. Cheklist pada opsi enabled kemudian isi
Primary Address: id.pool.ntp.org
Secondary Address: asia.pool.ntp.org


Lalu Apply, maka domain yang kita masukan akan ditranslasikan menjadi IP, dan beberapa parameter di bawahnya otomatis terisi. Artinya kita sudah terhubung dengan NTP server

Sekarang kita pastikan zona waktu pada router mikrotik kita judah sesuai dengan lokasi kita yaitu GMT+7 (Asia/Jakarta). Teman-teman yang di Indonesia Tengah dan Timur silakan menyesuaikan.
Buka menu System → Clock. Setting Timezone-nya sesuai daerah masing-masing. Contoh saya ada di Kebumen, Jawa Tengah. Maka saya set ke Asia/Jakarta. Kemudian klik apply. Perhatikan GMT Offsetnya harusnya sudah sesuai dengan lokasi kita. Demikian pula tanngal dan jamnya.


B. Konfigurasi Web Proxy
Setelah itu itu kita juga perlu mengaktifkan web proxy. Caranya buka menu IP → Web Proxy. Checklist opsi Enabled.
Port biarkan default 8080, checklist juga pada Anonymous, supaya bisa digunakan tanpa login. Cache administratornya ubah menjadi nama@sekolah.sch.id (i.e. webiptek@sekolah.sch.id).

Untuk web proxy, kita akan mengujinya di akhir, setelah semua konfigurasi soal ini selesai.


C. Konfigurasi DHCP Server untuk LAN dan WLAN
Berikutnya kita konfigurasi DHCP untuk ether2 (LAN) dan ether3 (WLAN). Keduanya ip poolnya diset untuk 99 client. Jadi ip pool pada ether2 kita gunakan 192.168.100.2-192.168.100.100 sedangkan pada ether3 kita gunakan 192.168.200.2-192.168.200.100.
Buka menu IP → DHCP Server, klik DHCP Setup. Kita mulai dari ether2. Biarkan parameternta default, kecuali pada Address to Giveout sesuaikan dengan ip pool di atas.


DHCP Address Space = 192.168.100.0/24
Gateway for DHCP Network = 192.168.100.1
Address to Giveout = 192.168.100.2-192.168.100.100
DNS Server = harus ada, boleh dicustom (contoh: 8.8.8.8, 1.1.1.1)
Lease time = 00:10:00
Lakukan hal serupa untuk konfigurasi dhcp server pada ether3. Jangan lupa ubah address to giveout-nya.
DHCP Address Space = 192.168.200.0/24
Gateway for DHCP Network = 192.168.200.1
Address to Giveout = 192.168.200.2-192.168.200.100
DNS Server = harus ada, boleh dicustom (contoh: 8.8.8.8, 1.1.1.1)
Lease time = 00:10:00

Kita bisa lakukan pengujian pada komputer client yang terhubung dengan kabel (jaringan LAN). Pastikan client mendapat ip di range 192.168.100.2-192.168.100.100 dan bisa terhubung ke internet.



Untuk client wireless akan kita uji nanti setelah konfigurasi wireless dan hotspot.

D. Konfigurasi Hotspot pada Router (ether3)
Jaringan LAN sudah tersambung, sekarang kita konfigurasi untuk jaringan wireless-nya. Pertama, kita buat hotspot server pada router untuk ether3. Hotspot di mikrotik nantinya berfungsi sebagai captive portal atau autentifikasi login user-password. Jadi setelah kita terhubung ke wifi, kita harus masuk atau autentifikasi lagi dengan user acccount. User account ini biasanya digunakan menentukan role atau profil kita. Gunanya role ini nanti bisa menentukan banyak hal, misalnya antara akun guru dan siswa bisa memiliki kecepatan akses, batas waktu koneksi, batas kuota yang berbeda-beda. Dan lainnya sesuai yang kita konfigurasi di mikrotik. Kita akan lihat penerapannya pada bagian Konfigurasi RADIUS untuk Hotspot

.
Buka menu IP → Hotspot, klik Hotspot Setup. Setting hotspot untuk interface ether3.


Biarkan semua parameternya default, kecuali bagian dns name, isi dengan nama domain untuk mengakses login page-nya nanti (i.e. ukk2020.id)

Sekarang kita sudah punya hotspot server untuk ether3 yang nanti akan diteruskan oleh Access Point.


E. Konfigurasi RADIUS Userman pada Router
Menurut wikipedia, RADIUS merupakan kependekan dari Remote Authentication Dial-In User Service adalah sebuah protokol keamanan komputer yang digunakan untuk melakukan autentikasi, otorisasi, dan pendaftaran akun pengguna secara terpusat untuk mengakses jaringan. Hehe.

Seperti yang saya katakan di bagian konfigurasi hotspot. Fitur hotspot mikrotik memungkinkan pengguna melakukan autentifikasi berupa user account untuk terhubung ke jaringan. Hotspot mikrotik sendiri ada management usernya, teman-teman bisa melihatnya di Soal Paket 2, di situ kita mengkonfigurasi user hotspot menggunakan fitur hotspot mikrotik. Jadi data akun user disimpan di mikrotik. Sementara itu dengan RADIUS kita menggunakan server khusus untuk menyimpan data user account tersebut.


Meskipun di lab ini, kita tidak akan menggunakan server luar. Kita akan menjadikan router mikrotik kita sebagai server RADIUS dengan package user-manager. Jadi nanti datanya tetap di simpan di mikrotik tapi dimanage oleh package user-manager bukan oleh package hotspot mikrotik seperti yang di soal paket 2. Tapi kegunaanya sama. Di userman kita juga mensetting parameter seperti limit kecepatan, masa aktif user, dsb.

Installasi User-Manager di Mikrotik

User-manager merupakan paket tambahan yang biasanya terpisah dari paket utama mikrotik (artinya: belum diinstall). Jadi, kita cek dulu apakah userman (begitu biasa user-manager disingkat) sudah terinstall. Caranya buka menu System → Pacakages.

Jika disitu tidak ada pacakage "user-manager", berarti belum diinstall dan kita harus menginstallnya.
Note: Tidak semua mikrotik support userman, karena untuk menginstall package tambahan perlu space pada memori penyimpanan. Mikrotik yang storage-nya hanya 16MB biasa tidak support.
BACA JUGA
Modul Mikrotik MTCINE BGP: Konsep BGP dan Implementasi BGP Attributes
Konfigurasi 6to4 Tunneling pada Cisco, Juniper, dan Mikrotik (IPv6 over IPv4 Protocol 41)
Cara Install Ulang Mikrotik Menggunakan Netinstall di Linux
Sebelum menginstall, kita perlu tahu apa type mikrotik kita dan versi berapa. Caranya cukup lihat top bar winbox. Di situ ada keterangan type dan versi mikrotik kita.

Jika sudah tahu type dan versi perangkat mikrotik kita. Sekarang kita download package user-manager di https://mikrotik.com/download. Pilih yangg extra package.

Jika sudah didownload, ekstrack filenya cari file "user-manager-xxx.npk" (xxx adalah versi routeros). Upload file tersebut ke mikrotik menggunakan FTP. Untuk windows bisa split screen winbox dan windows explorer kemudian drag and drop filenya ke winbox mikrotik, menu Files. Seperti gambar ini.


Setelah itu reboot mikrotik untuk menginstall packagenya. Pilih System → Reboot.

Akses mikrotik kembali setelah selesai reboot. Cek di System → Packages. Pastikan sekarang sudah terinstall package "user-manager".

Konfigurasi RADIUS untuk Hotspot Mikrotik

Buka menu RADIUS, tambah baru dengan klik icon "+". Di tab general. Pada bagian service, checklist hotspot.
Pada bagian Address isi dengan alamat ip server RADIUS. Karena kita menggunakan RADIUS di server lokal, maka ip addressnya kita dengan ip localhost, 127.0.0.1.
Untuk Secret, anggap saja itu adalah password untuk berinteraksi dengan server RADIUS. Jadi isi selayaknya password, contoh: ukk2020. Secret ini harus sama antara yang kita konfigurasi di mikrotik dan di server RADIUS (userman) nanti. Jadi catat baik-baik ya.

Masih di menu RADIUS, klik tombol Incoming. Checklist opsi Accept.

Kemudian pada menu IP → Hotspot → Server Profiles. Edit (double-click) user profile yang kita gunakan, pilih tab RADIUS, checklist User RADIUS.

Konfigurasi pada Server RADIUS

Buka ip mikrotik melalui web browser. Di sini saya menggunakan client jaringan LAN untuk mengaksesnya sehingga ip yang saya gunakan http://192.168.100.1/userman. Login ke userman dengan default login: admin tanpa password.

Tambahkan router client RADIUS. Caranya buka menu Router, klik Add New. Isi
Name: bebas (i.e. Router)
IP Address: alamat ip router (yaitu 172.0.0.1)
Shared Secret: isi sama dengan secret yang dikonfigurasi di mikrotik. (i.e. ukk2020).
Kemudian klik Add.

Membuat user di RADIUS (Userman)

Sebelum membuat user, kita buat profil terlebih dahulu. Profile inilah yang menentukan ketentuan atau role suatu user. Misalnya limit bandwidth, waktu akses internet, dll. Buka menu Profiles. Pilih tab Profiles, tambah baru dengan klik icon "+". Isi namanya terserah (i.e. profile1). Kemudian klik Create.

Masih di menu yang sama (Profiles), sekarang pilih tab Limitation. Klik Add New. Isi name: bebas (i.e. limit1). Kemudian klik Add.

Kembali ke tab Profiles. Tambahkan limitasi untuk profile1. Klik Add new limitation, pada bagian time kita atur waktu akses internetnya sesuai ketentuan soal yaitu dari pukul 07.00 sampai 16.00. Dan checklist pada limitasi yang tadi kita buat (i.e. limit1). Kemudian klik Add.

Save profile.

Sekarang kita buat 20 user dengan profile profile1. Buka menu Users, klik Add → Batch.
Number of user: isi dengan jumlah user yang akan dibuat yaitu 20.
Username prefix: isi nama awalan user (i.e. ukk).
Username lenght: menentukan panjang username (setelah prefix).
Password lenght: menentukan panjang password. Jangan lupa Assign profile ke profile1. Klik Add.

Maka akan ada 20 user baru, kita bisa gunakan user ini untuk login hotspot nanti. Double-click pada user tertentu untuk melihat passwordnya.




F. Opsi 1: Konfigurasi Access Point Mikrotik RouterOS
Mikrotik ini adalah perangkat yang fiturnya cukup lengkap. Meksipun router, mikrotik juga bisa dijadikan repeater jaringan kabel ke wireless atau dijadikan access point.

Saya asumsikan ether1 mkrotik access point ini terhubung ke ether3 router. Dan nanti kita gunakan wlan1 untuk access pointnnya.

Cara konfigurasinya, yang pertama adalah setting bridge. Kita gabungkan ether1 dan wlan1 agar mejadi switch port yang memungkinkan untuk meneruskan traffic layer-2.


Buka menu Bridge, pilih tab Bridge. Tambah baru dengan nama bebas (i.e. bridge1).

Masih di menu bridge, pilih tab Ports. Tambahkan ether1 dan wlan1 ke bridge bridge1.



Langkah berikutnya yaitu mengaktifkan wireless dan setup wifi pada wlan1.

Untuk mengaktifkan wlan1. Buka menu Wireless, pada tab WiFI Interface, klik pada wlan1 kemudian aktifkan dengan cara klik tombol centang biru.

Kemudian kita setup wifi pada wlan1. Masih di menu Wireless tab WiFI Interface. Edit (double-click) wlan1. Akan muncul jendela baru, pilih tab Wireless pada jendela baru tersebut.
Kemudian kita setting modenya menjadi ap_bridge. SSID-nya bebas karena tidak ada ketentuan (i.e. UKK-Paket3).

Jika semua sudah di konfigurasi pada Mikrotik Access Point. Sekarang bisa kita lakukan pengujian di sisi wireless client, bisa menggunakan Laptop atau Gawai (Smartphone).


G. Opsi 2: Konfigurasi Access Point TPLink
Opsi kedua selain menggunakan mikrotik, kita menggunakan Access Point yang konvensional, merk apapun. Di sini kita contohkan menggunakan TP Link TL-WA801ND. Untuk versi atau vendor/merk lain mungkah langkahnya sedikit berbeda tapi inti dari settingannya sama. Yaitu kita jadikan access point ini sebagai mode Access Point, hehe. Dan juga setting wifi (SSID dan password).

Cara Reset Access Point: biasanya cukup tekan dan tahan tombol reset dibagian belakang. Kemudian tunggu sampai semua LED di bagian depan nyala bersamaan semua. Setelah semua padam kembali, lepas tekanan di tombol reset.

Sumber: tp-link.com
Untuk mengakeses kita harus tau IP Address access point kita, yaitu dengan cara melihat di bagian bawah perangkat. Dengan catatan sudah direset ke default. Ini adalah bagian bawah access point TP-Link. Di situ default IP-nya: 192.168.0.1. User accessnya admin, password admin.

Sumber: itnesia.com
Untuk mengaksesnya kita hubungkan PC/Laptop ke port ethernet di accesspoint. Setelah itu setting dulu ip address PC/Laptop kita menjadi satu network dengan IP TP-Link 192.168.0.1/24. Misalnya IP Address PC kita setting ke 192.168.0.2 netmask 255.255.255.0. Setelah itu buka http://192.168.0.1 melalui browser. Kemudian login dengan name: admin, password: admin.

Pada web management access point, kita cari menu Quick Setup.

Jika muncul pertanyaan terkait mengganti password. Diisi jika ingin mengganti password, atau jika kita biarkan default dengan klik next.

Selanjutnya operation modenya kita setting ke: Access Point. Next.

Pada bagian wireless, kita setting SSID dan Securitynya. Untuk secuirty atau password bisa juga didisable. Bebas karena tidak ada ketentuan.

Pada bagian network setting, kita konfigurasi IP untuk access point. IP ini yang nanti akan digunakan untuk meremote accesspoint. Pilih yang static atau dinamis tidak masalah. Di sini saya konfigurasi secara statis. Isi dengan IP yang satu network dengan jaringan WLAN: 10.10.0.0/25. Misalnya kita setting ke ip access point tersebut ke 10.10.0.126 netmask 255.255.255.128. DHCP server saya disable karena sudah ada dari ROuter mikrotik.

Kemudian klik Next, kita di arahkan ke halaman rebiew. Kita cek konfigurasi kita apa sudah sesuai. Jika sudah klik finisih.



Maka access point akan rebooting, dan tunggu sampai selesai. Jangan lupa hubungkan kembali kabel dari access point ke ether3 router. Baru kemudian kita lakukan pengujian di sisi wireless client.
Pengujian Wireless Client

Hubungkan smartphone atau laptop ke wifi "webiptek@proxyUKK" (sesuai yang tadi dibuat).

Pastikan IP Address yang didapatkan sesuai dengan range-nya yaitu 192.168.200.2-192.168.200.100.

Kita akan diredirect ke halaman login. Jika tidak, coba buka secara manual halaman login melalui web browser http://ukk2020.id (sesuai dns name yang dikonfigurasi pada hotspot). Login menggunakan salah satu akun yang kita buat di RADIUS.

Jika berhasil masuk, cek juga koneksi internetnya, harusnya bisa.


Konfigurasi Firewall
Block ping dari IP 192.168.100.2-192.168.100.50 ke router

Buka menu IP → Firewall, pilih tab Firewall Filter. Tambah baru,
Chain = input
Src. Address = 192.168.100.2-192.168.100.50
Protocol = icmp
Action = Drop

Cara mengujinya buka client LAN, pastikan IP Addressnya ada di range 192.168.100.2-192.168.100.50, jika belum ubahlah menjadi IP di range tersebut secara statis (i.e. 192,168.100.10). Lalu ping ke client wireless (i.e. 192.168.200.99). Harusnya berhasil. Tetapi jika ping ke router (192.168.100.1) gagal, request timeout.

Block ping dari IP 192.168.100.51-192.168.100.100 ke Jaringan WLAN
Buka menu IP → Firewall, pilih tab Firewall Filter. Tambah baru,
Chain = forward
Src. Address = 192.168.100.51-192.168.100.100
Dst. Address = 192.168.200.0/24
Protocol = icmp
Action = Drop

Cara mengujinya buka client LAN, pastikan IP Addressnya ada di range 192.168.100.51-192.168.100.100, jika IP DHCP belum ada di range tersebut ubahlah menjadi IP di range tersebut secara statis (i.e. 192,168.100.100). Lalu ping ke client wireless (i.e. 192.168.200.99). Harusnya timeout. Tetapi jika ping ke router (192.168.100.1) berhasil.

Setiap Akses ke Router tercatat di Logging dan tersimpan di disk.

Buka menu IP → Firewall, pilih tab Firewall Filter. Tambah baru,
Chain = input
Action = log
Log = yes (checklist)
Log-prefix = akses-ke-router- (checklist)

Buka menu System → Logging, tambah rule baru. Isi prefix sesuai yang kita konfigurasi pada firewall. Pilih disk pada opsi Action.

Cara mengujinya cek menu Log, sekarang setiap ada traffic masuk akan dicatat di dalam log dengan prefix "akses-ke-router-" dan disimpan di disk.

Block Situs linux.org dan File .mp3 .mpk

Buka menu IP → Firewall, pili Filter Rules. Tambah baru.
chain = forward
content = linux.org
action = drop

Cara yang sama kita gunakan untuk memblokir file .mp3. Hanya perlu diubah content-nya.
chain = forward
content = .mp3
action = drop


Demikian juga untuk file .mkv.
chain = forward
content = .mkv
action = drop

Sekarang kalau kita akses situs linux.org mendowload file mp3 dan mkv, maka tidak bisa alias terblokir. Untuk filenya bisa didownload di http://ftp.vsudo.co.





Pengujian Web Proxy

Nah proxy bisa kita uji dengan cara mengkonfigurasikannya pada komputer client. Jika client menggunakan proxy maka dia akan terbebas dari firewall yang memblokir linux.org dan file mp3 mkv.

Cara konfigurasi proxy, buka google chrome. KLik icon titik 3 di pojok kanan atas. Kemudian pilih Setting, cari "Proxy". Pilih "Buka setelan proxy komputer Anda".

Contoh berikut ini adalah konfigurasi proxy di windows 10. Pada kolom Address isi alamat router. Sedangkan Port isi dengan port proxy yang kita gunakan tadi, yaitu 8080. Kemudian save.
