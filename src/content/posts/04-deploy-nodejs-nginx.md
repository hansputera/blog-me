---
title: Bagaimana cara setup Node.js App + NGINX di VPS?
description: Bagaimana sih setup project Node.js dengan webserver NGINX di VPS?
tags: [tech, technology, webserver, devops]
draft: false
image: "https://i.imgur.com/dvIbfaq.jpeg"
published: 2024-08-24
category: Technology
---

# Preambule
👋 Halo guys, terimakasih sudah berkunjung. Sebelum lebih lanjut ke gimana cara setupnya, mendingan kita ketahui dulu secara mendasar istilah teknologi yang akan kita gunakan nantinya.

## Lompat isi
1. [Bagaimana cara setup?](#bagaimana-cara-setupnya)
2. [Langkah-langkah](#langkah-langkah)
3. [Step 1 - SSH Command](#step-1---connect-ke-vps)
4. [Step 2 - Login to VPS](#step-2---verifikasi-fingerprint-dan-password)
5. [Step 3 - Installing NGINX & update dependencies](#step-3---install-nginx-dan-update-dependencies)
6. [Step 4 - Install Node.js](#step-4---install-nodejs)
7. [Step 5 - Konfigurasi Project](#step-5---konfigurasi-project-nodejs)
8. [Step 6 - Menjalankan Node.js App](#step-6---menjalankan-nodejs-app)
9. [Step 7 - Setup NGINX](#step-7---nginx-setup)
10. [Step 7.1 - Setup NGINX Direct IP dengan SSL](#direct-ip-dengan-ssl)
11. [Step 7.2 - Setup NGINX Domain tanpa SSL](#domain-dengantanpa-ssl)
12. [Step 7.3 - Setup NGINX Domain dengan SSL](#dengan-ssl)
13. [Penutup](#penutup)

## Apa itu VPS?
**VPS** (Virtual Private Server) adalah sebuah sistem yang memanfaatkan teknologi virtualisasi untuk membagi satu server fisik menjadi beberapa server virtual yang terpisah. Setiap server virtual ini memiliki sistem operasi dan sumber daya sendiri (seperti CPU, RAM, dan storage) yang dialokasikan secara eksklusif.

## Apa itu NGINX?
**NGINX**, singkatan dari "engine-x", adalah software open-source yang berfungsi sebagai server web, reverse proxy untuk mendistribusikan lalu lintas, load balancer untuk menyeimbangkan beban server, serta proxy email untuk protokol IMAP, POP3, dan SMTP.

## Apa itu Node.js?
> Node.js® is a free, open-source, cross-platform JavaScript runtime environment that lets developers create servers, web apps, command line tools and scripts.

Secara singkat, **Node.js** adalah runtime environment yang memungkinkan kita untuk menjalankan kode JavaScript di luar browser.

### Mengapa menggunakan NGINX?
Alasan saya pribadi menggunakan NGINX karena konfigurasinya yang cukup mudah dipahami, dibandingkan dengan kompetitornya webserver [Apache](https://apache.org). Selain itu, menurut saya NGINX lebih cepat dan ringan.

# Bagaimana cara setupnya?
Setelah kalian simak definisi diatas, sudah paham bukan teknologi yang akan kita gunakan? Tentunya dongg, hehehehe 😎

Sebelum memulai, tentunya kita membutuhkan beberapa persiapan, diantaranya:,
1. **server** atau **VPS** (Virtual Machine Server) yang akan digunakan untuk deployment (dapat dibeli di [Raznar](https://raznar.id), [IDCloudHost](https://idcloudhost.com), dan [Hostdata.ID](https://hostdata.id))
2. project node.js yang akan dijalankan (bisa dalam bentuk repository [GitHub](https://github.com))
3. terminal atau command prompt (pastikan dapat menggunakan perintah `ssh`, seperti gambar dibawah ya)
![Gambar SSH](https://i.imgur.com/nAtTx6L.png)

Dalam kasus ini, saya membeli VPS dari [IDCloudHost](https://idcloudhost.com) dengan spesifikasi sebagai berikut,
![Specifications server](https://i.imgur.com/ePGFa6v.png)

Setelah itu, kurang lebih teman-teman akan mendapatkan detil server yang sudah terbuat sebagai berikut,
![Server details](https://i.imgur.com/HotK00R.png)


## Langkah-langkah
### Step 1 - Connect ke VPS
Teman-teman silahkan membuka command prompt atau terminal favoritnya, lalu mengetik perintah sebagai berikut,
> `ssh [USERNAME VPS]@[IP VPS]`

:::note
Jangan lupa untuk mengganti `[USERNAME VPS]` dan `[IP VPS]` dengan username serta IP VPS-nya teman-teman. Tanpa `[` dan `]` yaa!
:::

### Step 2 - Verifikasi Fingerprint dan Password
Setelah teman-teman mengetik perintah diatas, akan ada permintaan _fingerprint verification_, silahkan teman-teman ketik `yes` atau enter saja. Seperti berikut,
![Fingerprint vps verification](https://i.imgur.com/htsFj2d.png)


Kemudian, ketika permintaan password telah muncul seperti diatas, maka silahkan input password VPS yang sesuai.

:::important
Jangan khawatir, jika password yang diketik tidak muncul, password tersamarkan secara otomatis sebagai bentuk keamanan.
:::

### Step 3 - Install NGINX dan Update Dependencies
Apabila teman-teman telah mengikuti [step 2](#step-2), maka teman-teman akan mendapati home screen seperti berikut,
![vps homescreen](https://i.imgur.com/qAKsZbc.png)

Silahkan teman-teman jalankan perintah dibawah ini,
```bash
sudo apt update
sudo apt upgrade

sudo apt install git nginx -y
```

- `sudo apt update`: Memperbarui daftar paket untuk pembaruan dan penginstallan.
- `sudo apt upgrade`: Memperbarui semua paket yang terinstal pada sistem ke versi terbaru.
- `sudo apt install git nginx -y`: Menginstall Git & NGINX. Flag -y secara otomatis mengkonfirmasi prompt penginstallan (yes/no).

### Step 4 - Install Node.js
Untuk instalasi Node.js, kita akan menggunakan [Node Version Manager (NVM)](https://github.com/nvm-sh/nvm), karena dengan tool ini akan memudahkan kita untuk switch version Node.js tanpa ribet-ribet lagi kedepannya.

::github{repo="nvm-sh/nvm"}

Silahkan copy perintah dibawah untuk menginstall [nvm](https://github.com/nvm-sh/nvm),
```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash

export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

Kurang lebih jika sudah terinstall, akan seperti gambar berikut apabila kita mengeceknya dengan mengetik perintah `nvm`,

![what nvm looks like?](https://i.imgur.com/mEQ6ZK4.png)

Apabila sudah terlihat seperti gambar diatas, silahkan jalankan perintah dibawah,
```bash
nvm install 21
```

:::note
`21` bisa diganti dengan versi Node.js lainnya, seperti `18`, `12`, `20`, `14` atau pun versi spesifiknya seperti `20.17.0`
:::

Jika proses instalasi sudah selesai, silahkan verifikasi dengan perintah `node -v`, apabila jika sudah sesuai dengan versi Node.js yang kalian request, maka proses instalasi sudah berjalan dengan sempurna 😎

### Step 5 - Konfigurasi Project Node.js
Selamat datang di step ke-5, kali ini kita akan membahas bagaimana kita mengkonfigurasi project node.js kita di dalam VPS ini.

Disini, saya akan menjalankan project Bookshelf API berikut dengan Node.js `v21.7.3`.
::github{repo="hansputera/bookshelf-api"}

Hal yang harus dilakukan adalah melakukan cloning repository tersebut ke VPS kita, dengan cara sebagai berikut,
```bash
git clone https://github.com/hansputera/bookshelf-api
cd bookshelf-api # masuk ke directory project tersebut dengan nama bookshelf-api
```
selanjutnya, kita akan melakukan instalasi dependencies project tersebut dengan perintah `npm i`.

Apabila instalasi dependencies sukses, kurang lebih akan muncul sebagai gambar dibawah,
![success npm i](https://i.imgur.com/e16apJh.png)

Selanjutnya, kita coba lihat dan ubah jika diperlukan dalam kode atau konfigurasi project Node.js kita, saran dari saya lebih baik apabila teman-teman mengakses environment dari Node.js `NODE_ENV` untuk mengecek apakah berstatus `production` atau `development` untuk menentukan binding server address ke `localhost` atau `0.0.0.0`
![example code](https://i.imgur.com/x6v7LsE.png)

### Step 6 - Menjalankan Node.js App
Apabila teman-teman telah mengikuti [step 5](#step-5---konfigurasi-project-nodejs), maka teman-teman dipersilahkan mengikuti step ini.

Tentunya kita tidak selalu membuka server atau VPS selama 24 jam ⏰ bukan? Sama aja boong kalau punya VPS, namun kita selalu buka device kita untuk mengonlinekan appnya 😂

Caranya gampang banget, kita bisa menggunakan tool [PM2](https://pm2.io) untuk menjalankan program atau app Node.js kita di background. Gimana? Simak dibawah.

Cara installnya gampang banget, perintahnya seperti dibawah ya
```bash
npm i -g pm2
```

Untuk verifikasi instalasi dapat mengetik perintah `pm2`, apabila muncul help info dari [pm2](https://pm2.io), maka dipastikan instalasi sukses.

Selanjutnya gimana? Masih di direktori project yang sama, kita jalankan perintah sebagai berikut,
```bash
pm2 start src/index.js --name "Nama Project"
pm2 save

pm2 startup
```

:::note
`Nama Project` bisa diganti dengan nama app sesuka hati kalian. Dan, `src/index.js` juga bisa diganti dengan lokasi file program Node.js yang akan dijalankan (misal program yang ingin dijalankan adalah `index.js`, maka ganti saja dengan `index.js`)

Command `pm2 save` berfungsi untuk menyimpan _state_ app yang kalian buat.

Sedangkan `pm2 startup` berfungsi untuk memberikan command kepada kita untuk dijalankan sehingga [pm2](https://pm2.io) akan berjalan otomatis walaupun VM di reboot.
![pm2 startup img](https://i.imgur.com/yJ2Nc2O.png)
:::

Apabila sukses, akan muncul output seperti gambar dibawah
![pm2 startup success](https://i.imgur.com/o0HMGso.png)

Udah selesai? Iya udah 😎 EH-nggak dong WKWK, kita belum setup NGINX nya loh 🤩😋

### Step 7 - NGINX Setup
Untuk konfigurasi NGINX-nya kita akan bagi menjadi dua, yaitu:,
1. Direct IP dengan SSL
2. Domain dengan/tanpa SSL

#### Direct IP dengan SSL
Tentunya sewaktu-waktu kita pengen akses app kita secara langsung tanpa harus ribet beli domain [example.com](https://example.com), abcd.com, inidomain.id, dan seterusnya supaya bisa diakses dengan SSL.

Secara default, jika menggunakan yang gratisan (_free_) yakni [letsencrypt](https://letsencrypt.org), jenis SSL ini hanya berfungsi pada alamat yang berdomain, dan tidak berlaku pada IP Address.

Salah satu solusi yang saya temukan adalah dengan menggunakan layanan SSL gratis oleh [ZeroSSL](https://zerossl.com/). Hanya dibutuhkan akun, dan alamat IP VM saja, kita bisa mendapatkan sertifikat SSL secara gratis.

##### ZeroSSL Verification
1. Kita mendaftar/signup atau registrasi akun di [ZeroSSL](https://zerossl.com/)
2. Ketika masuk ke bagian homepage setelah registrasi akun, klik "New Certificate"
![new cert](https://i.imgur.com/MIEHn2S.png)
3. Setelah itu, teman-teman akan masuk ke halaman form validasi. Silahkan isi field "Domain" dengan alamat IP VPS teman-teman, dan klik "Next Step"
![zerossl field domain](https://i.imgur.com/I8uqbAH.png)
4. Selanjutnya silahkan pilih validity "90-day" jika ingin yang gratis. Lalu klik "Next Step"
5. Untuk addons, "CSR & Contact", dan field finalize order, kita langsung skip saja dengan klik "Next Step" juga.
6. Nah, sampai juga dengan halaman verifikasi server. Kurang lebih tampilannya seperti berikut, silahkan pilih metode "HTTP File Upload"
![zerossl verif](https://i.imgur.com/vQlONgG.png)
![zerossl http file up](https://i.imgur.com/SMda75Q.png)
7. Silahkan teman-teman download file teks "Download File Auth", kemudian jalankan perintah dibawah di VPSnya teman-teman
```bash
sudo mkdir -p /var/www/well-known/pki-validation
```
:::note
Perintah `mkdir -p /var/www/well-known/pki-validation` berfungsi untuk membuat directory `/var/www/well-known/pki-validation`
:::

8. Jika sudah, silahkan teman-teman copy nama filenya  dan isinya yang sudah teman-teman download tadi. Dan, jalankan perintah dibawah,
```bash
sudo nano /var/www/well-known/pki-validation/NAMA_FILE_DICOPY
```

:::important
`NAMA_FILE_DICOPY` diganti dengan nama file yang sudah dicopy tadi, jangan lupa apabila berakhiran `.txt`, hal tersebut juga dimasukan. Intinya semua nama filenya dimasukan.
:::

9. Setelah itu, teman-teman silahkan paste isi file yang sudah dicopy tadi. Jika sudah, silahkan `CTRL+X`, dan apabila ditanya `Save modified buffer?`, maka ketik saja `Y` lalu enter.

10. Jika sudah, silahkan teman-teman copy teks berikut:
```nginx
 location /.well-known {
    autoindex on;
    alias /var/www/well-known;
 }
```
11. Lalu silahkan teman-teman edit kembali file `/etc/nginx/sites-available/default` dengan editor `nano`
```bash
nano /etc/nginx/sites-available/default
```
12. Kemudian scroll ke bawah, sehingga menemukan line seperti berikut
```nginx
location / {
    ....
}

# isi disini
```
:::important
Silahkan ganti `# isi disini` dengan konfigurasi yang telah dicopy sebelumnya.
:::

13. Jika konfigurasi kurang lebih seperti dibawah, silahkan `CTRL+X`, dan save. Lalu keluar. 
![zerossl nginx cfg](https://i.imgur.com/4r8AeX9.png)
14. Kembali ke halaman verifikasi [ZeroSSL](https://zerossl.com), lalu klik "Next Step", dan terakhir "Verify Domain"
15. Jika kamu mendapatkan gambar seperti dibawah, maka verifikasi kamu berhasil.
![verifikasi done zerossl](https://i.imgur.com/5onLNS7.png)
16. Jika proses pembuatan sertifikat SSL sudah selesai, silahkan pilih "Server Type" menjadi **NGINX**, dan download file `.zip` sertifikatnya.

##### NGINX Setup
1. Silahkan teman-teman copy konten file `certificate.crt` dan `private.key`.
:::important
- Silahkan buat file `certificate.pem` di folder `/etc/ssl/certs` dengan perintah sebagai berikut:
```bash
nano /etc/ssl/certs/certificate.pem
```

Lalu paste isi dari file `certificate.crt` di file `certificate.pem` tersebut.

- Lalu, lakukan hal yang sama dengan file `private.key` seperti cara diatas.
:::

2. Silahkan copy konfigurasi dibawah lalu paste ke `/etc/nginx/sites-available/app-reverse` menggunakan editor `nano`
```nginx
server {
        listen 80;
        listen [::]:80;

        server_name IP_VPS;
        return 301 https://$host$request_uri;
}

server {
        listen 443 ssl;
        listen [::]:443 ssl;

        server_name IP_VPS;

        ssl_certificate /etc/ssl/certs/certificate.pem;
        ssl_certificate_key /etc/ssl/certs/private.key;

        location / {
                proxy_pass http://127.0.0.1:5000/;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
        }
}
```
:::important
- Untuk `IP_VPS` silahkan diganti dengan IP VPSnya teman-teman.
- Untuk `http://127.0.0.1:5000`, angka `5000` bisa diganti dengan port app node.js-nya teman-teman.
:::
3. Kemudian, kita akan apply changes nginx dengan cara dibawah
```bash
sudo ln -s /etc/nginx/sites-available/app-reverse /etc/nginx/sites-enabled/app-reverse
sudo systemctl restart nginx
```
4. Enjoy! Sekarang IP VPS kalian bisa diakses secara langsung dengan HTTPs dan mengarah ke project Node.js-nya teman-teman 😎

#### Domain dengan/tanpa SSL
Cara disini kurang lebih hampir sama dengan [ini](#direct-ip-dengan-ssl)

##### Tanpa SSL
Tanpa SSL adalah paling gampang banget, karena konfigurasinya hanya seperti ini
```nginx
server {
    listen 80;
    listen [::]:80;

    server_name domain_kalian.id;

    location / {
        proxy_pass http://127.0.0.1:5000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

:::important
- Untuk `domain_kalian.id` silahkan diganti dengan domain milik teman-teman.
- Untuk `http://127.0.0.1:5000`, angka `5000` bisa diganti dengan port app node.js-nya teman-teman.
:::

Lalu sisa di paste ke file `/etc/nginx/sites-available/app-reverse-http` menggunakan editor `nano`. Kemudian jalankan perintah dibawah untuk mengaktifkannya,
```bash
sudo ln -s /etc/nginx/sites-available/app-reverse-http /etc/nginx/sites-enabled/app-reverse-http
sudo systemctl restart nginx
```


##### Dengan SSL
Kalau dengan SSL, gimana? Sama aja kayak [ini](#domain-dengantanpa-ssl), bedanya ditambah server 443 nya doang 😋

Jadi confignya gimana? Seperti dibawah ya
```nginx
server {
    listen 80;
    listen [::]:80;

    server_name domain_kalian.id;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    listen [::]:443 ssl;

    server_name domain_kalian.id;

    ssl_certificate /etc/ssl/certs/certificate.pem;
    ssl_certificate_key /etc/ssl/certs/private.key;

    location / {
        proxy_pass http://127.0.0.1:5000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Udah gitu doang, cuman butuh SSL Certificate aja dari [LetsEncrypt](https://letsencrypt.org), untuk tutorialnya bakal saya buatkan di artikel selanjutnya ✌️ ([Lihat Disini](/posts/05-generate-ssl-letsencrypt/))

Setelah itu, sisanya sama seperti cara di [NGINX Setup Domain - Tanpa SSL](#domain-dengantanpa-ssl) ❤️

# Penutup
Baiklah, mungkin itu saja petunjuk dan solusi yang dapat saya berikan dalam artikel ini. Dan, berikut dibawah merupakan hasil dari upaya diatas, connection ke IP VPS sudah menjadi secure karena sudah terpasang SSL certificate dari [ZeroSSL](https://zerossl.com).
![gambar jadi](https://i.imgur.com/7WlGGZQ.png)

Kurang lebih dan kesalahan kata, saya mohon maaf, tinggalkan komentar jika berkenan, sampai bertemu kembali di artikel selanjutnya 😋
