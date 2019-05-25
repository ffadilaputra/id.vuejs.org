---
title: Lebih Dalam Tentang Reaktifitas
type: guide
order: 601
---

Salah satu fitur Vue yang paling mencolok adalah sistem reaktivitas yang mudah digunakan. Model hanyalah objek JavaScript biasa. Saat kalian memodifikasinya, tampilan akan otomatis diperbarui. Sehingga manajemen state lebih sederhana dan intuitif, tetapi juga penting untuk memahami cara kerjanya untuk menghindari beberapa kesalahan umum. Pada bagian ini, kita akan belajar lebih dalam tentang sistem reaktifitas di Vue.

<div class="vue-mastery"><a href="https://www.vuemastery.com/courses/advanced-components/build-a-reactivity-system" target="_blank" rel="noopener" title="Vue Reactivity">Lihat video penjelasan di Vue Mastery</a></div>

## Bagaimana Properti Dipantau

Ketika kalian meneruskan objek JavaScript ke instan Vue menggunakan opsi `data`, Vue akan membaca semua properti yang ada di objek tersebut dan menjadikan tiap properti tersebut menjadi [getter/setter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects#Defining_getters_and_setters) dengan [Object.defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty). Fitur ini hanya tersedia di ES5, karena itulah Vue tidak mendukung IE8 kebawah.

Getter/setter tersebut disembunyikan dari usrer, tapi di balik layar, Vue menggunakannya untuk melacak properti mana saja yang berubah. Konsol browser akan menampilkan getter/setter dengna format yang berbeda ketika objek tersebut dilog, kalian mungkin bisa memasang [vue-devtools](https://github.com/vuejs/vue-devtools) untuk tampilan yang lebih mudah dibaca.

Setiap instan komponen memiliki instan **watcher**, yang merekam properti apa saja yang "tersentuh" oleh proses render komponen. Ketika salah satu properti tersebut berubah nilai, maka watcher akan dipanggil, setelah itu komponen akan dire-render.

![Siklus Reaktifitas](/images/data.png)

## Peringatan Seputar Pendeteksi Perubahan

Due to the limitations of modern JavaScript (and the abandonment of `Object.observe`), Vue **cannot detect property addition or deletion**. Since Vue performs the getter/setter conversion process during instance initialization, a property must be present in the `data` object in order for Vue to convert it and make it reactive. For example:

Karena keterbatasn JavaScript modern (dan ditinggalkanya `Object.observe`), Vue **tidak bisa mendeteksi penambahan atau penghapusan properti**. Karena Vue mengatur getter/setter ketika instan pertama kali diinisialisasi, maka properti tersebut harus ada di dalam objek `data` sehingga Vue bisa mendeteksinya. Contoh:

``` js
var vm = new Vue({
  data: {
    a: 1
  }
})
// `vm.a` sekarang reaktif

vm.b = 2
// `vm.b` tidak reaktif
```

Vue does not allow dynamically adding new root-level reactive properties to an already created instance. However, it's possible to add reactive properties to a nested object using the `Vue.set(object, propertyName, value)` method:

Vua tidak membolehkan penambahan properti reaktif di tingkat root secara dinamis di instan yang sudah dibuat. Tetapi, kalian bisa menggunakan `Vue.set(object, key, value)` untuk menambahkan properti baru.

``` js
Vue.set(vm.someObject, 'b', 2)
```

You can also use the `vm.$set` instance method, which is an alias to the global `Vue.set`:

Kalian juga bisa menggunakan method instan `vm.$set`, sebagai alias dari `Vue.set` global:

``` js
this.$set(this.someObject, 'b', 2)
```

Kadang, kalian butuh menambahkan banyak properti ke objek yang sudah ada, contohnya ketika menggunakan `Object.assign()` atau `_.extend()`. Tetapi, properti baru yang ditambahkan tidak akan reaktif. Untuk kasus ini, buat objek baru dengan properti dari objek orisinil dan objek yang akan ditambahkan:

``` js
// daripada `Object.assign(this.someObject, { a: 1, b: 2 })`
this.someObject = Object.assign({}, this.someObject, { a: 1, b: 2 })
```

Ada juga beberapa pengecualian untuk array, yang bisa dipelajari di [bagian list rendering](list.html#Caveats).

## Cara Deklarasi Properti Reaktif

Since Vue doesn't allow dynamically adding root-level reactive properties, you have to initialize Vue instances by declaring all root-level reactive data properties upfront, even with an empty value:

Karena Vue tidak membolehkan penambahan properti reaktif secara dinamis di level root, kalian harus menginisialisasi semua properti reaktif di awal, meskipun nilainya kosong:

``` js
var vm = new Vue({
  data: {
    // buat properti message dengan nilai string kosong
    message: ''
  },
  template: '<div>{{ message }}</div>'
})
// set `message`
vm.message = 'Hello!'
```

Jika kalian tidak mendeklarasikan `message` terlebih dahulu, Vue akan memberikan peringatan bahwa fungsi render hendak mengakses properti yang tidak ada.

Ada beberapa alasan teknis dibalik keterbatasan ini - tidak perlu adanya pengecualian khusus untuk sistem deteksi perubahan, dan juga membuat Vue bisa bekerja baik dengan sistem pengecekan tipe. Selain itu, ada pertimbangan khusus untuk membuat aplikasi lebih mudah diatur: objek `data` berfungsi sebagai skema dari state komponen kalian. Menuliskan properti yang reaktif di awal membuat code kita lebih mudah untuk dipahami oleh pengembang lain.

## Antrian Pembaharuan Asinkronus

Vue melakukan pembaharuan DOM secara **asinkronus**. Setiap ada perubahan data, maka perubahan tersebut akan dimasukkan ke dalam antrian yang terjadi di dalam event loop yang sama. Jika sebuah watcher dipanggil beberapa kali, dia hanya akan dimasukan ke dalam antrian satu kali saja. Ini penting supaya tidak ada kalkulasi yang duplikat yang seharusnya tidak perlu dipanggil. Di "tick" event loop selanjutnya, Vue akan memproses queue yang ada. Secara internal, Vue menggunakan `Promise.then`, `MutationObserver`, dan `setImmediate` untuk antrian asinkronus ini, dan menggunakan `setTimeout(fn, 0)` jika keduanya tidak tersedia.

Contohnya, ketika kalian set `vm.someData = 'new value'`, komponen tidak akan langsung melakukan proses re-render. Rerender akan terjadi di "tick" selanjutnya, ketika antrian diproses. Biasanya, kalian tidak perlu terlalu memikirkan tentang proses ini. Tapi ini bermanfaat jika kalian ingin melakukan operasi yang tergantung dengan keadaan DOM yang terbaru. Meskipun Vue mendorong pengembang untuk mengemban konsep "data-driven" dan menghindari mengotak-atik DOM secara langsung. Jika kalian harus melakukannya, Vue menyediakan `Vue.nextTick(callback)` yang akan dipanggil setelah data diproses. Contohnya:

``` html
<div id="example">{{ message }}</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    message: '123'
  }
})
vm.message = 'new message' // rubah data
vm.$el.textContent === 'new message' // false
Vue.nextTick(function () {
  vm.$el.textContent === 'new message' // true
})
```

There is also the `vm.$nextTick()` instance method, which is especially handy inside components, because it doesn't need global `Vue` and its callback's `this` context will be automatically bound to the current Vue instance:

Kalian juga bisa menggunakan `vm.$nextTick()`, yang bisa digunakan di dalam komponen, `this` secara otomatis akan dibind ke instan Vue yang aktif:

``` js
Vue.component('example', {
  template: '<span>{{ message }}</span>',
  data: function () {
    return {
      message: 'not updated'
    }
  },
  methods: {
    updateMessage: function () {
      this.message = 'updated'
      console.log(this.$el.textContent) // => 'not updated'
      this.$nextTick(function () {
        console.log(this.$el.textContent) // => 'updated'
      })
    }
  }
})
```

Karena `$nextTick()` mengembalikan sebuah promise, kalian bisa menggunakan sintaks [async](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)

``` js
  methods: {
    updateMessage: async function () {
      this.message = 'updated'
      console.log(this.$el.textContent) // => 'not updated'
      await this.$nextTick()
      console.log(this.$el.textContent) // => 'updated'
    }
  }
```
