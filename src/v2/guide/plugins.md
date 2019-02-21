---
title: Plugin
type: guide
order: 304
---

Umumnya, plugin digunakan untuk menambahkan fungsionalitas Vue di level global. Tidak ada batasan tertentu untuk plugin. Berikut contoh beberapa plugin:

1. Menambahkan method dan properti global. Contoh [vue-custom-element](https://github.com/karol-f/vue-custom-element)
1. Menambahkan aset global seperti directive/filter/transition/dsb. Contoh [vue-touch](https://github.com/vuejs/vue-touch)
1. Menambahkan opsi komponen menggunakan mixin global. Contoh [vue-router](https://github.com/vuejs/vue-router)
1. Menambahkan method instan dengan menambahkannya melalui `Vue.prototype`.
1. Library yang menyediakan API-nya sendiri dan juga menggunakan beberapa metode di atas. Contoh [vue-router](https://github.com/vuejs/vue-router).

## Cara Menggunakan Plugin

Gunakan plugin dengan memanggil `Vue.use()`. Plugin hanya bisa dipanggil sebelum kalian memanggil `new Vue()`:

``` js
// panggil `MyPlugin.install(Vue)`
Vue.use(MyPlugin)

new Vue({
  //... opsi
})
```

Kalian bisa meneruskan beberapa opsi:

``` js
Vue.use(MyPlugin, { someOption: true })
```

`Vue.use` hanya memanggil plugin yang ada sekali aja. Jika kalian memanggil plugin yang sama berkali-kali, Vue hanya akan memanggilnya sekali saja.

Beberapa plugin seperti `vue-router` akan memanggil `Vue.use()` secara otomatis jika `Vue` terdeteki sebagai variabel global. Tetapi, di lingkungan modul seperti CommonJS, kalian harus secara manual memanggil `Vue.use()`.

``` js
// Ketika memakai CommonJS via Browserify atau Webpack
var Vue = require('vue')
var VueRouter = require('vue-router')

// Pastikan untuk memanggil Vue.use secara manual
Vue.use(VueRouter)
```

Lihat [awesome-vue](https://github.com/vuejs/awesome-vue#components--libraries) untuk daftar plugin dan library yang sudah dibuat oleh komunitas Vue.

## Cara Membuat Plugin

Plugin Vue.js harus menyediakan method `install`. Method ini yang akan dipanggil oleh Vue ketika `Vue.use()` dipanggil

``` js
MyPlugin.install = function (Vue, options) {
  // 1. tambah method dan properti global
  Vue.myGlobalMethod = function () {
    // kode kalian ...
  }

  // 2. tambah aset global
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // kode kalian ...
    }
    ...
  })

  // 3. tambah beberapa opsi komponen
  Vue.mixin({
    created: function () {
      // kode kalian ...
    }
    ...
  })

  // 4. tambah method instan
  Vue.prototype.$myMethod = function (methodOptions) {
    // kode kalian ...
  }
}
```
