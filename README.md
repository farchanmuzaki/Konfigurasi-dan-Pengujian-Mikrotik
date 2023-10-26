# Konfigurasi-dan-Pengujian-Mikrotik
Ini adalah page untuk belajar konfigurasi dasar dan pengujian

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/95690bcf-85a5-4bbd-a811-cdadd6726d4a)

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

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/71d6794b-5ad3-4c8a-8d54-e68052352397)


A. Konfigurasi IP Address, NTP dan NAT Masquerade pada Router
Kita mulai dengan request dhcp client pada ether1.
Buka menu IP → DHCP Client, tambah baru dengan klik icon "+". Pilih interfacenya ether1.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/63100c6a-5beb-41b3-888d-98dbc055a823)


Sehingga ether1 mendapat IP secara otomatis.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/fc213c3f-3b8e-4a49-b267-868834336be2)


Selanjutnya adalah konfigurasi IP address untuk ether2 (LAN) dan ether3 (WLAN), ketentuanya:
ether2 = 192.168.100.1/25
ether3 = 192.168.200.1/24
Caranya buka menu IP → Addresses, tambah baru dengan klik icon "+". Masukan ip address dan interface sesuai ketentuan di soal.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/dd396d51-dfcc-4d12-89f1-058623a577b8)

Sekarang ether1-3 sudah dikonfigurasi alamat IP.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/dfa6dd3e-eb20-4b24-8e47-008e140f5693)

Berikutnya coba cek koneksi dari mikrotik ke internet dengan ping. Buka menu New Terminal pada winbox mikrotik.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/74cb1e3c-9086-4ecd-b5c1-1c60e6525c75)


Jika masih gagal, cek default gateway (buka IP → Route) dan DNS server (buka IP → DNS).

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/77376891-4258-423b-8f21-24c1bbb80270)


Jika Router sudah terhubung ke internet, selanjutnya agar client bisa terhubung ke internet juga nantinya, kita perlu setting NAT Masquarede.
Buka IP → Firewall → NAT, tambah rule baru dengan parameter yang diisi sebagai berikut.
Chain: scrcnat
Out. Inteface: ether1 (interface yang terhubung ke arah internet)
Action: masquerade

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/0fc872e8-2b62-4edf-817e-2c71578f2c68)

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/601d4430-bb98-451c-b7a9-fcac8a54da16)


Selain itu kita juga diminta konfiguras NTP (Network Time Protocol) untuk sinkronisasi waktu. Caranya buka menu System → SNTP Client. Cheklist pada opsi enabled kemudian isi
Primary Address: id.pool.ntp.org
Secondary Address: asia.pool.ntp.org

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/28cd9c41-37a8-4f5e-8b02-a42aae488ac5)


Lalu Apply, maka domain yang kita masukan akan ditranslasikan menjadi IP, dan beberapa parameter di bawahnya otomatis terisi. Artinya kita sudah terhubung dengan NTP server

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/2ce88b78-5a88-49cf-be9e-179d78bed5c6)


Sekarang kita pastikan zona waktu pada router mikrotik kita judah sesuai dengan lokasi kita yaitu GMT+7 (Asia/Jakarta). Teman-teman yang di Indonesia Tengah dan Timur silakan menyesuaikan.
Buka menu System → Clock. Setting Timezone-nya sesuai daerah masing-masing. Contoh saya ada di Kebumen, Jawa Tengah. Maka saya set ke Asia/Jakarta. Kemudian klik apply. Perhatikan GMT Offsetnya harusnya sudah sesuai dengan lokasi kita. Demikian pula tanngal dan jamnya.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/2f144148-71d2-4f82-9f91-526fde84cea0)


B. Konfigurasi Web Proxy
Setelah itu itu kita juga perlu mengaktifkan web proxy. Caranya buka menu IP → Web Proxy. Checklist opsi Enabled.
Port biarkan default 8080, checklist juga pada Anonymous, supaya bisa digunakan tanpa login. Cache administratornya ubah menjadi nama@sekolah.sch.id (i.e. webiptek@sekolah.sch.id).

Untuk web proxy, kita akan mengujinya di akhir, setelah semua konfigurasi soal ini selesai.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/331ec021-3d77-4e2c-8587-351fa206fdbc)


C. Konfigurasi DHCP Server untuk LAN dan WLAN
Berikutnya kita konfigurasi DHCP untuk ether2 (LAN) dan ether3 (WLAN). Keduanya ip poolnya diset untuk 99 client. Jadi ip pool pada ether2 kita gunakan 192.168.100.2-192.168.100.100 sedangkan pada ether3 kita gunakan 192.168.200.2-192.168.200.100.
Buka menu IP → DHCP Server, klik DHCP Setup. Kita mulai dari ether2. Biarkan parameternta default, kecuali pada Address to Giveout sesuaikan dengan ip pool di atas.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/da66d1e3-e77f-464d-b2c0-99ea4f4f2397)


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

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/494f35fb-fea6-4167-9d69-180b48733d60)


Kita bisa lakukan pengujian pada komputer client yang terhubung dengan kabel (jaringan LAN). Pastikan client mendapat ip di range 192.168.100.2-192.168.100.100 dan bisa terhubung ke internet.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/bdf2aa56-aeba-4a04-892b-20a34a131f27)


Untuk client wireless akan kita uji nanti setelah konfigurasi wireless dan hotspot.

D. Konfigurasi Hotspot pada Router (ether3)
Jaringan LAN sudah tersambung, sekarang kita konfigurasi untuk jaringan wireless-nya. Pertama, kita buat hotspot server pada router untuk ether3. Hotspot di mikrotik nantinya berfungsi sebagai captive portal atau autentifikasi login user-password. Jadi setelah kita terhubung ke wifi, kita harus masuk atau autentifikasi lagi dengan user acccount. User account ini biasanya digunakan menentukan role atau profil kita. Gunanya role ini nanti bisa menentukan banyak hal, misalnya antara akun guru dan siswa bisa memiliki kecepatan akses, batas waktu koneksi, batas kuota yang berbeda-beda. Dan lainnya sesuai yang kita konfigurasi di mikrotik. Kita akan lihat penerapannya pada bagian Konfigurasi RADIUS untuk Hotspot

.
Buka menu IP → Hotspot, klik Hotspot Setup. Setting hotspot untuk interface ether3.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/f741d18d-e428-49e5-bb5d-84522c4bf1ac)

Biarkan semua parameternya default, kecuali bagian dns name, isi dengan nama domain untuk mengakses login page-nya nanti (i.e. ukk2020.id)

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/606f981a-b4ca-40ed-a133-0b78bb3f27c1)


Sekarang kita sudah punya hotspot server untuk ether3 yang nanti akan diteruskan oleh Access Point.


E. Konfigurasi RADIUS Userman pada Router
Menurut wikipedia, RADIUS merupakan kependekan dari Remote Authentication Dial-In User Service adalah sebuah protokol keamanan komputer yang digunakan untuk melakukan autentikasi, otorisasi, dan pendaftaran akun pengguna secara terpusat untuk mengakses jaringan. Hehe.

Seperti yang saya katakan di bagian konfigurasi hotspot. Fitur hotspot mikrotik memungkinkan pengguna melakukan autentifikasi berupa user account untuk terhubung ke jaringan. Hotspot mikrotik sendiri ada management usernya, teman-teman bisa melihatnya di Soal Paket 2, di situ kita mengkonfigurasi user hotspot menggunakan fitur hotspot mikrotik. Jadi data akun user disimpan di mikrotik. Sementara itu dengan RADIUS kita menggunakan server khusus untuk menyimpan data user account tersebut.


Meskipun di lab ini, kita tidak akan menggunakan server luar. Kita akan menjadikan router mikrotik kita sebagai server RADIUS dengan package user-manager. Jadi nanti datanya tetap di simpan di mikrotik tapi dimanage oleh package user-manager bukan oleh package hotspot mikrotik seperti yang di soal paket 2. Tapi kegunaanya sama. Di userman kita juga mensetting parameter seperti limit kecepatan, masa aktif user, dsb.

Installasi User-Manager di Mikrotik

User-manager merupakan paket tambahan yang biasanya terpisah dari paket utama mikrotik (artinya: belum diinstall). Jadi, kita cek dulu apakah userman (begitu biasa user-manager disingkat) sudah terinstall. Caranya buka menu System → Pacakages.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/173456f8-3a62-45c6-9a6a-d40ed6189d54)


Jika disitu tidak ada pacakage "user-manager", berarti belum diinstall dan kita harus menginstallnya.
Note: Tidak semua mikrotik support userman, karena untuk menginstall package tambahan perlu space pada memori penyimpanan. Mikrotik yang storage-nya hanya 16MB biasa tidak support.

Sebelum menginstall, kita perlu tahu apa type mikrotik kita dan versi berapa. Caranya cukup lihat top bar winbox. Di situ ada keterangan type dan versi mikrotik kita.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/7f26eaf9-31d0-4036-90f3-85e6aa8e9073)

Jika sudah tahu type dan versi perangkat mikrotik kita. Sekarang kita download package user-manager di https://mikrotik.com/download. Pilih yangg extra package.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/60e35737-8b88-4c78-9a4f-6c12cbbafa7f)


Jika sudah didownload, ekstrack filenya cari file "user-manager-xxx.npk" (xxx adalah versi routeros). Upload file tersebut ke mikrotik menggunakan FTP. Untuk windows bisa split screen winbox dan windows explorer kemudian drag and drop filenya ke winbox mikrotik, menu Files. Seperti gambar ini.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/fd560535-5af4-44fa-9fe5-fe44e6494cf4)


Setelah itu reboot mikrotik untuk menginstall packagenya. Pilih System → Reboot.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/cb9f4139-0e9e-494a-bb21-414b9b30c764)


Akses mikrotik kembali setelah selesai reboot. Cek di System → Packages. Pastikan sekarang sudah terinstall package "user-manager".

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/4b5fc0b4-82d6-4d8d-8ab6-95d20d48b063)


Konfigurasi RADIUS untuk Hotspot Mikrotik

Buka menu RADIUS, tambah baru dengan klik icon "+". Di tab general. Pada bagian service, checklist hotspot.
Pada bagian Address isi dengan alamat ip server RADIUS. Karena kita menggunakan RADIUS di server lokal, maka ip addressnya kita dengan ip localhost, 127.0.0.1.
Untuk Secret, anggap saja itu adalah password untuk berinteraksi dengan server RADIUS. Jadi isi selayaknya password, contoh: ukk2020. Secret ini harus sama antara yang kita konfigurasi di mikrotik dan di server RADIUS (userman) nanti. Jadi catat baik-baik ya.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/18042c2b-ef88-4434-9ba5-fc7a2b4af69b)


Masih di menu RADIUS, klik tombol Incoming. Checklist opsi Accept.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/c2c7f722-39f7-499d-bb04-706095c7da39)


Kemudian pada menu IP → Hotspot → Server Profiles. Edit (double-click) user profile yang kita gunakan, pilih tab RADIUS, checklist User RADIUS.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/9f548322-e734-4d9e-a25f-67f861485d3b)


Konfigurasi pada Server RADIUS

Buka ip mikrotik melalui web browser. Di sini saya menggunakan client jaringan LAN untuk mengaksesnya sehingga ip yang saya gunakan http://192.168.100.1/userman. Login ke userman dengan default login: admin tanpa password.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/d4e60de5-635e-409e-988f-8225484026c5)


Tambahkan router client RADIUS. Caranya buka menu Router, klik Add New. Isi
Name: bebas (i.e. Router)
IP Address: alamat ip router (yaitu 172.0.0.1)
Shared Secret: isi sama dengan secret yang dikonfigurasi di mikrotik. (i.e. ukk2020).
Kemudian klik Add.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/8738d098-5047-4981-b969-b2dab7e1503a)


Membuat user di RADIUS (Userman)

Sebelum membuat user, kita buat profil terlebih dahulu. Profile inilah yang menentukan ketentuan atau role suatu user. Misalnya limit bandwidth, waktu akses internet, dll. Buka menu Profiles. Pilih tab Profiles, tambah baru dengan klik icon "+". Isi namanya terserah (i.e. profile1). Kemudian klik Create.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/30ee3b56-315f-46a1-b1c1-91fd49e6d5a0)

Masih di menu yang sama (Profiles), sekarang pilih tab Limitation. Klik Add New. Isi name: bebas (i.e. limit1). Kemudian klik Add.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/5c6dc9bf-ee79-46af-92ac-75ceef126d6d)

Kembali ke tab Profiles. Tambahkan limitasi untuk profile1. Klik Add new limitation, pada bagian time kita atur waktu akses internetnya sesuai ketentuan soal yaitu dari pukul 07.00 sampai 16.00. Dan checklist pada limitasi yang tadi kita buat (i.e. limit1). Kemudian klik Add.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/b71fb18d-40f2-4121-b427-7792d0cb3dc3)

Save profile.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/44fc3cf1-4307-46ff-89e7-0a3c8b05159f)

Sekarang kita buat 20 user dengan profile profile1. Buka menu Users, klik Add → Batch.
Number of user: isi dengan jumlah user yang akan dibuat yaitu 20.
Username prefix: isi nama awalan user (i.e. ukk).
Username lenght: menentukan panjang username (setelah prefix).
Password lenght: menentukan panjang password. Jangan lupa Assign profile ke profile1. Klik Add.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/535beb57-613f-45e3-8bca-de3fbaaeca25)

Maka akan ada 20 user baru, kita bisa gunakan user ini untuk login hotspot nanti. Double-click pada user tertentu untuk melihat passwordnya.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/5811b6ee-3a53-49db-b108-1fe4e1e16a6f)

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/d65712a7-de8c-4e11-a32a-fdf65cb1a3ad)


F. Opsi 1: Konfigurasi Access Point Mikrotik RouterOS
Mikrotik ini adalah perangkat yang fiturnya cukup lengkap. Meksipun router, mikrotik juga bisa dijadikan repeater jaringan kabel ke wireless atau dijadikan access point.

Saya asumsikan ether1 mkrotik access point ini terhubung ke ether3 router. Dan nanti kita gunakan wlan1 untuk access pointnnya.

Cara konfigurasinya, yang pertama adalah setting bridge. Kita gabungkan ether1 dan wlan1 agar mejadi switch port yang memungkinkan untuk meneruskan traffic layer-2.


Buka menu Bridge, pilih tab Bridge. Tambah baru dengan nama bebas (i.e. bridge1).

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/ef03b2f5-19dc-4c98-824e-acd7755f1bd3)

Masih di menu bridge, pilih tab Ports. Tambahkan ether1 dan wlan1 ke bridge bridge1.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/c1aa3e0c-f02a-4a39-843b-b6bcaaffabbb)

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/fe5e9185-f394-4f36-96f9-03e2cfc30f87)

Langkah berikutnya yaitu mengaktifkan wireless dan setup wifi pada wlan1.

Untuk mengaktifkan wlan1. Buka menu Wireless, pada tab WiFI Interface, klik pada wlan1 kemudian aktifkan dengan cara klik tombol centang biru.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/82b84ac2-94ba-42f7-9acc-fee9c0f4b34f)

Kemudian kita setup wifi pada wlan1. Masih di menu Wireless tab WiFI Interface. Edit (double-click) wlan1. Akan muncul jendela baru, pilih tab Wireless pada jendela baru tersebut.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/4824aa59-c917-4700-b1b5-95154465dabd)

Kemudian kita setting modenya menjadi ap_bridge. SSID-nya bebas karena tidak ada ketentuan (i.e. UKK-Paket3).

Jika semua sudah di konfigurasi pada Mikrotik Access Point. Sekarang bisa kita lakukan pengujian di sisi wireless client, bisa menggunakan Laptop atau Gawai (Smartphone).


G. Opsi 2: Konfigurasi Access Point TPLink
Opsi kedua selain menggunakan mikrotik, kita menggunakan Access Point yang konvensional, merk apapun. Di sini kita contohkan menggunakan TP Link TL-WA801ND. Untuk versi atau vendor/merk lain mungkah langkahnya sedikit berbeda tapi inti dari settingannya sama. Yaitu kita jadikan access point ini sebagai mode Access Point, hehe. Dan juga setting wifi (SSID dan password).

Cara Reset Access Point: biasanya cukup tekan dan tahan tombol reset dibagian belakang. Kemudian tunggu sampai semua LED di bagian depan nyala bersamaan semua. Setelah semua padam kembali, lepas tekanan di tombol reset.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/60976e48-33f8-4e24-bfa9-fe2441fea69e)


Sumber: tp-link.com
Untuk mengakeses kita harus tau IP Address access point kita, yaitu dengan cara melihat di bagian bawah perangkat. Dengan catatan sudah direset ke default. Ini adalah bagian bawah access point TP-Link. Di situ default IP-nya: 192.168.0.1. User accessnya admin, password admin.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/95bb150b-2192-475d-87a0-a37c98f70962)

Sumber: itnesia.com
Untuk mengaksesnya kita hubungkan PC/Laptop ke port ethernet di accesspoint. Setelah itu setting dulu ip address PC/Laptop kita menjadi satu network dengan IP TP-Link 192.168.0.1/24. Misalnya IP Address PC kita setting ke 192.168.0.2 netmask 255.255.255.0. Setelah itu buka http://192.168.0.1 melalui browser. Kemudian login dengan name: admin, password: admin.

Pada web management access point, kita cari menu Quick Setup.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/cc335281-ccf9-491d-82c7-83811a8b8466)

Jika muncul pertanyaan terkait mengganti password. Diisi jika ingin mengganti password, atau jika kita biarkan default dengan klik next.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/17743403-46e9-4aef-9b2c-7971d9c0de66)

Selanjutnya operation modenya kita setting ke: Access Point. Next.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/1acb52fa-0326-4a7f-aaed-be7b172bd8dc)

Pada bagian wireless, kita setting SSID dan Securitynya. Untuk secuirty atau password bisa juga didisable. Bebas karena tidak ada ketentuan.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/20f3cf28-07b2-4d07-81d1-1f0b82b648bc)

Pada bagian network setting, kita konfigurasi IP untuk access point. IP ini yang nanti akan digunakan untuk meremote accesspoint. Pilih yang static atau dinamis tidak masalah. Di sini saya konfigurasi secara statis. Isi dengan IP yang satu network dengan jaringan WLAN: 10.10.0.0/25. Misalnya kita setting ke ip access point tersebut ke 10.10.0.126 netmask 255.255.255.128. DHCP server saya disable karena sudah ada dari ROuter mikrotik.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/ff790f9d-4f0b-4353-9a32-772332584001)

Kemudian klik Next, kita di arahkan ke halaman rebiew. Kita cek konfigurasi kita apa sudah sesuai. Jika sudah klik finisih.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/1086fc49-a9aa-478b-8668-a8b00fc2be62)

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/eda0118a-c379-4900-b25f-99664c797492)

Maka access point akan rebooting, dan tunggu sampai selesai. Jangan lupa hubungkan kembali kabel dari access point ke ether3 router. Baru kemudian kita lakukan pengujian di sisi wireless client.
Pengujian Wireless Client

Hubungkan smartphone atau laptop ke wifi "webiptek@proxyUKK" (sesuai yang tadi dibuat).

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/2e52c7db-4f29-4d3b-8385-a008f812bdc9)

Pastikan IP Address yang didapatkan sesuai dengan range-nya yaitu 192.168.200.2-192.168.200.100.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/241e90ed-d0da-415d-a6b1-a9d7948e6838)

Kita akan diredirect ke halaman login. Jika tidak, coba buka secara manual halaman login melalui web browser http://ukk2020.id (sesuai dns name yang dikonfigurasi pada hotspot). Login menggunakan salah satu akun yang kita buat di RADIUS.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/6b1f2469-43d0-4607-b1f0-d2bf68b2dcb2)

Jika berhasil masuk, cek juga koneksi internetnya, harusnya bisa.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/3965deb5-96ef-471d-a250-5a573fe01323)

Konfigurasi Firewall
Block ping dari IP 192.168.100.2-192.168.100.50 ke router

Buka menu IP → Firewall, pilih tab Firewall Filter. Tambah baru,
Chain = input
Src. Address = 192.168.100.2-192.168.100.50
Protocol = icmp
Action = Drop

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/f4a3015c-52ba-4f73-82ce-4078582dd53c)

Cara mengujinya buka client LAN, pastikan IP Addressnya ada di range 192.168.100.2-192.168.100.50, jika belum ubahlah menjadi IP di range tersebut secara statis (i.e. 192,168.100.10). Lalu ping ke client wireless (i.e. 192.168.200.99). Harusnya berhasil. Tetapi jika ping ke router (192.168.100.1) gagal, request timeout.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/bf9ff75d-23eb-4c16-a923-9104c88bcc78)

Block ping dari IP 192.168.100.51-192.168.100.100 ke Jaringan WLAN
Buka menu IP → Firewall, pilih tab Firewall Filter. Tambah baru,
Chain = forward
Src. Address = 192.168.100.51-192.168.100.100
Dst. Address = 192.168.200.0/24
Protocol = icmp
Action = Drop

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/f029f33f-8e61-4f2c-8d11-fdddeaa07215)

Cara mengujinya buka client LAN, pastikan IP Addressnya ada di range 192.168.100.51-192.168.100.100, jika IP DHCP belum ada di range tersebut ubahlah menjadi IP di range tersebut secara statis (i.e. 192,168.100.100). Lalu ping ke client wireless (i.e. 192.168.200.99). Harusnya timeout. Tetapi jika ping ke router (192.168.100.1) berhasil.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/1c6ba972-d4d3-438e-8c07-7d6a76440466)

Setiap Akses ke Router tercatat di Logging dan tersimpan di disk.

Buka menu IP → Firewall, pilih tab Firewall Filter. Tambah baru,
Chain = input
Action = log
Log = yes (checklist)
Log-prefix = akses-ke-router- (checklist)

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/dc604dc4-49a8-4629-b8c7-5c2cdba955d7)

Buka menu System → Logging, tambah rule baru. Isi prefix sesuai yang kita konfigurasi pada firewall. Pilih disk pada opsi Action.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/0163acd7-28f1-4d64-b1dc-a994fc135c99)

Cara mengujinya cek menu Log, sekarang setiap ada traffic masuk akan dicatat di dalam log dengan prefix "akses-ke-router-" dan disimpan di disk.


![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/14f7381d-9db3-42e7-b322-b39a9fd82096)

Block Situs linux.org dan File .mp3 .mpk

Buka menu IP → Firewall, pili Filter Rules. Tambah baru.
chain = forward
content = linux.org
action = drop


![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/e9c47308-db7a-4baa-ba96-72fd6c8d94d7)

Cara yang sama kita gunakan untuk memblokir file .mp3. Hanya perlu diubah content-nya.
chain = forward
content = .mp3
action = drop

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/a16e5d06-60c2-41c9-90df-9a423d16724d)


Demikian juga untuk file .mkv.
chain = forward
content = .mkv
action = drop


![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/4ffc8ada-4a8d-44d2-babf-49a1a5836afd)

Sekarang kalau kita akses situs linux.org mendowload file mp3 dan mkv, maka tidak bisa alias terblokir. Untuk filenya bisa didownload di http://ftp.vsudo.co.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/fd21b477-41fc-422b-b097-2354a18be25e)

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/100c361d-ebc2-4b51-82a9-819416c5fa81)

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/23cb3524-b5ae-42ac-b55e-56c550f8973a)

Pengujian Web Proxy

Nah proxy bisa kita uji dengan cara mengkonfigurasikannya pada komputer client. Jika client menggunakan proxy maka dia akan terbebas dari firewall yang memblokir linux.org dan file mp3 mkv.

Cara konfigurasi proxy, buka google chrome. KLik icon titik 3 di pojok kanan atas. Kemudian pilih Setting, cari "Proxy". Pilih "Buka setelan proxy komputer Anda".

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/06c7a117-78fe-4f8d-a825-bf21b1660bbe)

Contoh berikut ini adalah konfigurasi proxy di windows 10. Pada kolom Address isi alamat router. Sedangkan Port isi dengan port proxy yang kita gunakan tadi, yaitu 8080. Kemudian save.

![image](https://github.com/farchanmuzaki/Konfigurasi-dan-Pengujian-Mikrotik/assets/116914974/d84a1ee0-6adc-48b3-810a-195357e36dbb)

Nah sekarang kita bisa mengakses linux.org, serta mendownload file .mp3 dan .mkv.
