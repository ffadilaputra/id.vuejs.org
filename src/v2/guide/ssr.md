---
title: Rendering di Sisi Server
type: guide
order: 503
---

## Panduan Lengkap Tentang SSR

Rendering di Sisi Server (_Server Side Rendering_ / SSR) adalah mekanisme untuk melakukan proses render di sisi server. Dengan menggunakan SSR, kode Vue yang dikirimkan dari server sudah dirubah menjadi bentuk HTML.

Kami sudah membuat panduan khusus yang membahas Rendering di Sisi Server untuk aplikasi Vue. Panduan lengkap ini cocok untuk kalian yang sudah familiar dengan Vue di sisi klien, pengembangan server Node.js, dan webpack. Lihat panduannya di [ssr.vuejs.org](https://ssr.vuejs.org/) (_panduan dalam Bahasa Inggris_).

## Nuxt.js

Konfigurasi Rendering di Sisi Server bukanlah hal yang mudah. Untungya, ada sebuah proyek dari komunitas yang mencoba menyelesaikan permasalahan ini. Salah satunya adalah [Nuxt.js](https://nuxtjs.org/). Nuxt.js adalah framework yang dibangun di atas ekosistem Vue. Ditujukan untuk membuat pengembangan aplikasi Vue universal lebih mudah. Lebih kerennya lagi, kalian bisa menggunakannya sebagai _static site generator_ (dengan halaman yang ditulis dalam bentuk Komponen Satu Berkas / _Single File Component_ / SFC)! Kami sarankan kalian untuk mencobanya.

## Quasar Framework SSR + PWA

[Quasar Framework](https://quasar-framework.org/) akan menghasilkan aplikasi SSR (yang bisa dijadikan PWA) dengan _build system_ terbaik, konfigurasi yang masuk akal, dan bisa dimodifikasi oleh penggunanya. Dengan sekitar 100 komponen yang menerapkan konsep "Material Design 2.0", kalian bisa menentukan komponen mana yang diproses di server, dan komponen mana yang diproses di browser. Kalian juga bisa merubah tag `<meta>` dari situs kalian dengan framework ini. Quasar adalah _development environment_ berbasis Node.js dan Webpack yang bisa membantu kalian mempercepat proses pembuatan aplikasi SPA, PWA, SSR, Electron, dan Cordova; dengan satu basis kode yang sama.