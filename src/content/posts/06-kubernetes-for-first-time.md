---
title: Kubernetes for 1st time (setup)
description: Using K8S or Kubernetes for first time
tags: [devops, technology]
category: Technology
draft: true
published: 2024-09-29
image: https://raw.githubusercontent.com/kubernetes/kubernetes/refs/heads/master/logo/logo.png
---
# Preambule
Yo hallo! Jadi pada postingan kali ini, saya mau sharing sedikit pengalaman saya pertama kali mau install Kubernetes di VPS/VM.
Kenapa saya belajar Kubernetes? Karena, dalam beberapa project freelance saya sepertinya architecture mendekati Microservices.
Sehingga, mau tidak mau saya harus belajar tool seperti Docker dan Kubernetes lebih lanjut demi memudahkan saya kedepannya.

# Apa itu Kubernetes?
Kubernetes merupakan software DevOps untuk memudahkan kita untuk melakukan deployment application ke beberapa server. Apa bedanya sama Docker? Dari pemahaman sederhana saya,
Docker tuh secara sederhana, bisa ibarat orang yang kerja, dan Kubernetes ini adalah managernya. Atau, bisa juga dibilang Docker ini worker dan Kubernetes itu Bosnya.

## Alasan menggunakan Kubernetes
Alasan saya untuk menggunakan Kubernetes ialah kebutuhan saya rata-rata yang dihandle sendiri, ternyata bisa automatic handling. Dengan apa? Yaps, dengan Kubernetes.
Contohnya semisal saya punya project YouTube Downloader, saya punya 2 app secara garis besar. App 1 untuk Worker Controller, dan App 2 sebagai Worker.
Sisa appnya bisa Redis, dan PostgreSQL.

### High workload/traffic
Ok, lets say traffic lagi tinggi, kemudian workload meningkat. Apa yang harus saya lakukan? Yap, membuat server baru lagi untuk menanganinya.
Kemudian, saya harus ngatur lagi di webserver untuk loadbalancer dan hal ribet lainnya.

Alhasil saya mau kalau task saya ini dilakukan dengan otomatis. Oke, kita bisa pake Docker untuk sementara waktu untuk horizontal scalingnya.
Namun, disini kasusnya saya adalah YouTube Downloader, bisa dibilang IP VPS/VMnya harus banyak untuk menghindari blokir dari YouTube (IP Rotating),
 otomatis saya ada beberapa server yang harus tersedia di beberapa region. Kemudian, saya mau buat lagi otomatis gateway untuk antar server ini.
 Secara harfiah, saya harus setup segala macem kalau ada server baru seperti loadbalancer, docker, deploy app, dan lain sebagainya. (RIBET OY!)

Dan, itu mengubah mindset saya sebelumnya memaksimalkan fungsi dari Docker, alhasil saya harus belajar tool baru untuk lebih memudahkan saya. Pada akhirnya
saya memutuskan untuk menggunakan K8S atau Kubernetes yang saya pikir udah mencakup segala kebutuhan saya saat ini yang dibutuhkan :)

# Instalasi Kubernetes
Ada sedikit perbedaan instalasi untuk production environment dengan local environment. Apa?
1. Local environment, cuman butuh **Docker**, **Minikube**, dan **Kubectl** and u go.
2. Production environment, sebenernya banyak sih. Cuman awalannya yang saya dapat butuhnya **Docker**, **Kubeadm**, **Kubectl**, **Kubelet**, dan **Cri-DockerD**

Ya sedikit lebih banyak jika untuk prod environment, namun kalau ada yang kurang bisa lampirkan di giscuss dibawah ya :^)
