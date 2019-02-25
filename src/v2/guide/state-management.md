---
title: Manajemen State
type: guide
order: 502
---

## Implementasi Resmi Yang Mirip Flux

Kadang aplikasi yang kalian kembangkan bisa berkembang menjadi besar dan rumit, karena banyak _state_ dan interaksi yang tersebar di berbagai tempat. Untuk mengatasi masalah ini, Vue menawarkan [vuex](https://github.com/vuejs/vuex): _library_ manajemen state milik Vue yang terinspirasi oleh Elm. Vuex terintegrasi dengan [vue-devtools](https://github.com/vuejs/vue-devtools), dengan fitur [time travel debugging](https://raw.githubusercontent.com/vuejs/vue-devtools/master/media/demo.gif).

<div class="vue-mastery"><a href="https://www.vuemastery.com/courses/mastering-vuex/intro-to-vuex/" target="_blank" rel="noopener" title="Tutorial Vuex">Lihat penjelasan video di Vue Mastery</a></div>

### Informasi untuk Pengguna React

Jika kalian sebelumnya biasa menggunakan React, kalian mungkin berfikir apa perbedaan antara Vuex dan [redux](https://github.com/reactjs/redux). Redux adalah library kecil yang tidak peduli tentang masalah tampilan. Sehingga Redux sendiri bisa digunakan bersamaan dengan Vue menggunakan [binding sederhana](https://yarnpkg.com/en/packages?q=redux%20vue&p=1). Perbedaan Vuex dengan Redux adalah, Vuex sudah pasti digunakan dengan aplikasi Vue. Sehingga Vuex lebih terintegrasi dengan Vue dan memberikan pengalaman pengembangan yang lebih baik.

## Dasar Manajemen State Sederhana

Kadang sering terlewatkan bahwa sumber kebenaran utama aplikasi Vue adalah objek `data`. Instan Vue hanya memberikan perantara untuk mengaksesnya. Jadi, kalau kalian mempunyai data yang ingin dibagi dengan beberapa instan, kalian bisa membagikannya seperti di bawah:

``` js
const sourceOfTruth = {}

const vmA = new Vue({
  data: sourceOfTruth
})

const vmB = new Vue({
  data: sourceOfTruth
})
```

Ketika `sourceOfTruth` berubah, `vmA` dan `vmB` akan merubah tampilannya secara otomatis. Subkomponen di bawah instan ini juga bisa mengaksesnya lewat `this.$root.$data`. Kita sudah punya sumber kebenaran utama sekarang. Tapi cara ini sulit untuk didebug. Data kita bisa berubah-ubah dari bagian aplikasi mana saja, tanpa meninggalkan jejak yang jelas siapa yang merubah dan apa yang berubah.

Sebagai solusi masalah ini, kita bisa mengadopsi **_store_ pattern**:

``` js
var store = {
  debug: true,
  state: {
    message: 'Hello!'
  },
  setMessageAction (newValue) {
    if (this.debug) console.log('setMessageAction triggered with', newValue)
    this.state.message = newValue
  },
  clearMessageAction () {
    if (this.debug) console.log('clearMessageAction triggered')
    this.state.message = ''
  }
}
```

Perhatikan bahwa semua aksi untuk merubah state dari store tersebut ada di dalam store itu sendiri. Jenis manajemen state yang terpusat seperti ini mempermudah kita untuk memahaim jenis perubahan apa yang terjadi. Ketika ada kesalahan, kita bisa melacaknya dengan lebih mudah.

Sebagai tambahan, tiap instan/komponen juga bisa memiliki state pribadinya sendiri:

``` js
var vmA = new Vue({
  data: {
    privateState: {},
    sharedState: store.state
  }
})

var vmB = new Vue({
  data: {
    privateState: {},
    sharedState: store.state
  }
})
```

![Manajemen State](/images/state.png)

<p class="tip">Jangan sampai kalian menindih objek yang ada di state semula. Komponen butuh mengakses _reference_ ke objek yang sama untuk mendeteksi perubahan.</p>

Seiring dengan perkembangan aturan bahwa komponen tidak boleh secara langsung merubah state yang ada di dalam sebuah store, dan hanya boleh melalui aksi yang disediakan oleh store. Pada akhirnya kita menerapkan arsitektur [Flux](https://facebook.github.io/flux/). Keuntungan dari aturan ini adalah kita bisa mencatat semua perubahan state, dan membantu proses debugging sehingga kita bisa memiliki _logs_, _snapshots_, dan _time travel_ untuk riwayat yang ada.

Akhirnya kembali lagi bahwa semua ini disediakan oleh [vuex](https://github.com/vuejs/vuex), jika kalian sudah membaca sejauh ini, mungkin ini saatnya untuk mencobanya!
