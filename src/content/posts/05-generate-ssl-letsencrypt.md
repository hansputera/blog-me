---
title: Membuat TLS/SSL Certificate Secara Gratis Dengan Letsencrypt
description: Hari ini gini masih HTTP? Beralih ke HTTPS dong, lebih secure 😎😋
tags: [tech, technology, webserver, devops]
draft: false
image: "https://i.imgur.com/75TW7Vv.png"
published: 2024-08-25
category: Technology
---

# Preambule
Hola-hola, have a nice day everyone ✌️❤️ Pada artikel ini saya akan sharing gimana sih buat TLS certificate untuk domain kita secara gratis selamanya 🤔

## Apa itu TLS/SSL?
**TLS** merupakan singkatan dari _Transport Layer Secure_, yakni pengembangan lanjut dari **SSL** (_Socket Secure Layer_). Namun, pada saat ini kebanyakan penggunaan istilah SSL digunakan untuk menjelaskan konsep sederhana dari TLS. Jadi, teknologi yang sekarang kita gunakan adalah TLS ya! 😎

# Langkah-langkah
Udah, gas aja lah ya 😂 Selamat disimak ╰(*°▽°*)╯

## Persiapan
Hal-hal yang perlu disiapkan antara lain adalah:,
1. domain (bebas beli dimana aja pak)
2. vps/server (ini juga bebas beli dimana aja, lebih baik lagi kalau udah dapat IPv4 sendiri pak)

## Step 1 - Install Webserver NGINX & Certbot
Tanpa basa-basi, langsung gas bae 😎 Kita install dulu nih webservernya, kita bakalan pake [nginx](https://nginx.org) lagi cuy.
Dan, of course ada tool baru nih, kenalin [certbot](https://certbot.eff.org). **Cerbot** merupakan tool CLI (Command-Line Interface) yang digunakan di server sistem UNIX (salah satunya GNU/Linux), fungsinya apa? Untuk membantu kita dalam generate SSL [LetsEncrypt](https://letsencrypt.org) cuy ✌️

Untuk installnya gimana?
```bash
sudo apt install nginx certbot python3-certbot-nginx -y
```

Udah gitu aja 😏

:::caution
Di beberapa kasus repository biasanya ada yang missing, silahkan update terlebih dahulu dengan cara berikut (jika Debian based)
```bash
sudo apt update
sudo apt upgrade
```
:::

## Step 2 - Konfigurasi NGINX
Di step ini kita akan konfigurasi NGINX untuk protokol HTTP terlebih dahulu. Kenapa? Karena certbot akan mengaturnya untuk kita nanti 😎 Oke lets say kita mau reverse proxy ke app di VPS kita yang berjalan di port `5000`, maka ip address reversenya adalah `http://127.0.0.1:5000`

Maka konfigurasinya kurang lebih akan seperti ini (silahkan buat file baru di `/etc/nginx/sites-available/`), misal saya buat dengan nama file `/etc/nginx/sites-available/app-reverse-http`
```nginx
server {
    listen 80;
    listen [::]:80;

    server_name domainkalian.id;

    location / {
        proxy_pass http://127.0.0.1:5000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

:::important
- Jangan lupa ganti `domainkalian.id` dengan domain milik kalian.
- Dan, jangan lupa juga mengganti `5000` pada proxy_pass untuk menyesuaikan port app yang berjalan.
:::

Lalu untuk pengaktifannya bisa melalui command-command dibawah,
```bash
sudo ln -s /etc/nginx/sites-available/app-reverse-http /etc/nginx/sites-enabled/app-reverse-http
sudo nginx -t # apakah sudah aman?

sudo systemctl restart nginx
```

:::important
Silahkan ganti `app-reverse-http` dengan nama file konfigurasi NGINX kalian masing-masing yang telah dibuat ya!
:::

## Step 3 - Tambah DNS Record Pada Domain
Agar cerbot dapat bekerja untuk mengeluarkan sertifikat TLS, maka diperlukannya verifikasi bahwa domain name yang di request telah aktif dan telah pointing ke server VPS. Silahkan menuju DNS Managementnya masing-masing, saya pribadi menggunakan DNS Managementnya [Cloudflare](https://cloudflare.com).

Untuk data recordnya kurang lebih seperti gambar dibawah ya guys ya ✌️
![dns cloudflare](https://i.imgur.com/LkLCrZU.png)

## Step 4 - Eksekusi certbot
Kalau kita udah pointing DNS Recordnya ke server VPSnya, ini udah gampang. Sisa kita eksekusi langsung aja si certbotnya 😏
Commandnya gimana?
```bash
sudo certbot -d domainkalian.id --nginx
```

Udah, singkat kan? 😋 Nah, silahkan teman-teman isi saja field yang diminta oleh certbotnya (seperti e-mail, yes dalam ToS, dan yes/no dikirim mailing oleh letsencrypt)

![sukses letsencrypt ni bos](https://i.imgur.com/yZIvIu8.png)
![sukses letsencrypt dns](https://i.imgur.com/LaAh8y0.png)

Jika teman-teman sudah mendapatkan output seperti gambar diatas, maka pembuatan sertifikat TLS telah berhasil 😏

# Penutup
Kurang lebihnya itu saja yang dapat saya sampaikan, mohon maaf kurang dan lebihnya kata, see you next time ya, ehee ✌️
![kaori bye bye](https://media1.tenor.com/m/g7aTr2MQ7KkAAAAC/kaori-kaori-peace.gif)