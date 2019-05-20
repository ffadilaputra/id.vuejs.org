---
title: Dasar-dasar Komponen
type: guide
order: 11
---

## Contoh Dasar

Berikut contoh untuk komponen Vue:

``` js
// Buat komponen baru dengan nama button-counter
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
```

Komponen adalah instan Vue yang mempunyai nama dan bisa digunakan kembali: dalam kasus ini, `<button-counter>`. Kita bisa menggunakan komponen ini sebagai elemen kustom di dalam instan _root_ Vue yang dibuat menggunakan `new Vue`:

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
```

{% codeblock lang:js %}
new Vue({ el: '#components-demo' })
{% endcodeblock %}

{% raw %}
<div id="components-demo" class="demo">
  <button-counter></button-counter>
</div>
<script>
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count += 1">You clicked me {{ count }} times.</button>'
})
new Vue({ el: '#components-demo' })
</script>
{% endraw %}

Karena komponen adalah instan Vue yang bisa digunakan kembali, mereka menerima opsi sama seperti `new Vue`, seperti `data`, `computed`, `watch`, `methods`, dan _lifecycle hook_; kecuali opsi spesifik untuk root seperti `el`.

## Menggunakan Komponen Berulang Kali

Komponen bisa digunakan berulang kali semau kalian:

```html
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

{% raw %}
<div id="components-demo2" class="demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
<script>
new Vue({ el: '#components-demo2' })
</script>
{% endraw %}

Perhatikan ketika kalian menekan tombol yang ada, setiap tombol memiliki `count`-nya sendiri-sendiri karena setiap kalian memanggil komponen, **instan baru** akan dibuat.


### `data` Harus Dalam Bentuk Fungsi

Ketika kita mendefinisikan komponen `<button-counter>`, kita mendefenisikan data tidak dalam bentuk objek, seperti:

```js
data: {
  count: 0
}
```

**Opsi `data` harus dalam bentuk fungsi**, sehingga setiap instan bisa memiliki data objek masing-masing:

```js
data: function () {
  return {
    count: 0
  }
}
```

Jika Vue tidak menerapkan aturan ini, satu tombol yang ditekan akan merubah semua data _instan lainnya juga_, seperti:

{% raw %}
<div id="components-demo3" class="demo">
  <button-counter2></button-counter2>
  <button-counter2></button-counter2>
  <button-counter2></button-counter2>
</div>
<script>
var buttonCounter2Data = {
  count: 0
}
Vue.component('button-counter2', {
  data: function () {
    return buttonCounter2Data
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
new Vue({ el: '#components-demo3' })
</script>
{% endraw %}

## Mengorganisir Komponen

Umumnya, aplikasi bisa diorganisir menjadi pohon komponen:

![Component Tree](/images/components.png)

Contoh, kalian punya komponen seperti _header_, _sidebar, dan area isi; di setiapnya bisa berisi komponen lain untuk navigasi, tulisan blog, dan sebgainya.

Untuk menggunakan komponen seperti itu di templat, komponen tersebut harus didaftarkan sehingga Vue bisa mendeteksinya. Ada dua jenis pendaftaran komponen: **global** dan **lokal**. Sejauh ini, kita hanya mendaftarkan komponen secara global, melalui `Vue.comopnent`:

```js
Vue.component('my-component-name', {
  // ... options ...
})
```

Komponen yang didaftarkan secara global bisa digunakan di dalam templat di root instan Vue mana saja yang dibuat setelahnya -- termasuk di subkomponen instan Vue tersebut.

Jika ingin mengetahui lebih lanjut, baca [Pendaftaran Komponen](components-registration.html).

## Meneruskan Data ke Komponen Anak Melalui Props

Sebelumnya, kita membicarakan tentang membuat komponen untuk tulisan blog. Masalahnya, komponen tersebut tidak begitu bermanfaat tanpa data yang bisa diteruskan ke dalamnya, seperti judul atau isi dari tulisan yang ingin ditampilkan. Saat inilah kita bisa menggunakan props.

Props adalah atribut kustom yang bisa didaftarkan di sebuah komponen. Ketika sebuah nilai diteruskan melalui atribut prop, dia menjadi properti dari instan komponen tersebut. Untuk meneruskan judul ke komponen tulisan blog kita, kita bisa mendaftarkannya sebagai props, melalui opsi `props`:


```js
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
```

Sebuah komponen bisa memiliki jumlah props yang tidak terbatas, dan pada dasarnya semua nilai bisa diteruskan melalui prop. Di templat atas, kita bisa mengakses nilai dari prop tersebut, mirip seperti saat mengakses `data`.


Setelah prop didaftarkan, kalian bisa meneruskan data dengan cara atribut kustom, seperti:

```html
<blog-post title="My journey with Vue"></blog-post>
<blog-post title="Blogging with Vue"></blog-post>
<blog-post title="Why Vue is so fun"></blog-post>
```

{% raw %}
<div id="blog-post-demo" class="demo">
  <blog-post1 title="My journey with Vue"></blog-post1>
  <blog-post1 title="Blogging with Vue"></blog-post1>
  <blog-post1 title="Why Vue is so fun"></blog-post1>
</div>
<script>
Vue.component('blog-post1', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
new Vue({ el: '#blog-post-demo' })
</script>
{% endraw %}

Umumnya, kalian bisa membuat daftar post di dalam `data`:

```js
new Vue({
  el: '#blog-post-demo',
  data: {
    posts: [
      { id: 1, title: 'My journey with Vue' },
      { id: 2, title: 'Blogging with Vue' },
      { id: 3, title: 'Why Vue is so fun' }
    ]
  }
})
```

Dan untuk setiap tulisan:

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:title="post.title"
></blog-post>
```

Di atas, kita menggunakan `v-bind` untuk meneruskan data dinamis ke props. Bermanfaat jika kalian tidak tahu secara pasti apa yang harus ditampilkan di awal, seperti saat [mengambil tulisan dari API](https://jsfiddle.net/chrisvfritz/sbLgr0ad).

Jika ingin mempelajari lebih lanjut tentang prop, baca [Prop](components-props.html).

## Elemen Satu Akar / Root

Ketika membuat komponen `<blog-post>`, templat kalian tidak hanya akan berisi judul saja seperti di bawah:

```html
<h3>{{ title }}</h3>
```

Paling tidak, kalian butuh isi dari tulisannya juga:

```html
<h3>{{ title }}</h3>
<div v-html="content"></div>
```

Tetapi, templat seperti ini akan menghasilkan error, yang menjelaskan bahwa **semua komponen hanya boleh punya satu elemen akar /root**. Kalian bisa menuliskannya seperti:

```html
<div class="blog-post">
  <h3>{{ title }}</h3>
  <div v-html="content"></div>
</div>
```

Seiring dengan berkembangnya komponen, bisa jadi kalian butuh lebih dari sekedar judul dan isi dari tulisan, tapi juga tanggal, komentar, dsb. Menuliskannya satu per satu sangat menyebalkan:

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:title="post.title"
  v-bind:content="post.content"
  v-bind:publishedAt="post.publishedAt"
  v-bind:comments="post.comments"
></blog-post>
```

Jadi, ini saat yang bagus untuk merubahnya dengan menerima prop `post`:

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:post="post"
></blog-post>
```

```js
Vue.component('blog-post', {
  props: ['post'],
  template: `
    <div class="blog-post">
      <h3>{{ post.title }}</h3>
      <div v-html="post.content"></div>
    </div>
  `
})
```

<p class="tip">Contoh di atas menggunakan fitur baru JavaScript, yaitu [template literal](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) untuk membuat templat panjang lebih mudah dibaca. Tetapi tidak didukung oleh Internet Explorer (IE), jadi jika kalian butuh mendukung IE dan tidak menggunakan transpiler (contoh. Babel atau TypeScript), gunakan [newline escapes](https://css-tricks.com/snippets/javascript/multiline-string-variables-in-javascript/).</p>

Sekarang, setiap properti di tambahkan ke objek `post`, secara otomatis bisa digunakan juga di dalam `<blog-post>`.

## Memantau Event dari Komponen Anak

Seiring berkembangnya komponen `<blog-post>`, beberapa fitur mungkin mengharuskan adanya komunikasi dari komponen-komponen di bawah `<blog-post>` ke komponen `<blog-post>` itu sendiri. Contohnya, kita ingin menambahkan fitur aksesibilitas untuk memperbesar teks di tulisan blog, tanpa merubah ukuran teks lain di halaman:

Di komponen parent, kita bisa menambahkan properti data `postFontSize`:

```js
new Vue({
  el: '#blog-posts-events-demo',
  data: {
    posts: [/* ... */],
    postFontSize: 1
  }
})
```

Yang bisa digunakan di dalam templat untuk mengatur ukuran font di semua blog post:

```html
<div id="blog-posts-events-demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
      v-for="post in posts"
      v-bind:key="post.id"
      v-bind:post="post"
    ></blog-post>
  </div>
</div>
```

Sekarang, mari kita coba tambahkan tombol untuk memperbesar teks di setiap post:

```js
Vue.component('blog-post', {
  props: ['post'],
  template: `
    <div class="blog-post">
      <h3>{{ post.title }}</h3>
      <button>
        Enlarge text
      </button>
      <div v-html="post.content"></div>
    </div>
  `
})
```

Masalahnya, tombol ini masih belum bisa berfungsi:

```html
<button>
  Enlarge text
</button>
```

Ketika kita menekan tombol tersebut, kita harus berkomunikasi dengan `parent` yang akan merubah ukuran teksnya. Untungnya, VUe menyediakan sistem _custom event_ untuk menyelesaikan masalah ini. Parent bisa menunggu sebuah event terjadi di komponen anaknya dengan `v-on`, sama seperti penggunaan di native DOM:

```html
<blog-post
  ...
  v-on:enlarge-text="postFontSize += 0.1"
></blog-post>
```

Lalu komponen anaknya bisa membuat event dengan memanggil [method **`$emit`**](../api/#vm-emit), dengan meneruskan nama eventnya:

```html
<button v-on:click="$emit('enlarge-text')">
  Enlarge text
</button>
```

Dengan adanya listener `v-on:enlarge-text="postFontSize += 0.1"`, parentnya bisa menerima event dan merubah nilai dari `postFontSize`.

{% raw %}
<div id="blog-posts-events-demo" class="demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
      v-for="post in posts"
      v-bind:key="post.id"
      v-bind:post="post"
      v-on:enlarge-text="postFontSize += 0.1"
    ></blog-post>
  </div>
</div>
<script>
Vue.component('blog-post', {
  props: ['post'],
  template: '\
    <div class="blog-post">\
      <h3>{{ post.title }}</h3>\
      <button v-on:click="$emit(\'enlarge-text\')">\
        Enlarge text\
      </button>\
      <div v-html="post.content"></div>\
    </div>\
  '
})
new Vue({
  el: '#blog-posts-events-demo',
  data: {
    posts: [
      { id: 1, title: 'My journey with Vue', content: '...content...' },
      { id: 2, title: 'Blogging with Vue', content: '...content...' },
      { id: 3, title: 'Why Vue is so fun', content: '...content...' }
    ],
    postFontSize: 1
  }
})
</script>
{% endraw %}

### Mengirim Nilai Dengan Event

Kadang kita butuh mengirim sebuah nilai dengan event. Contohnya, kita ingin komponen `<blog-post>` bertanggung jawab untuk menentukan seberapa besar teks tersebut harus dirubah. Dalam kasus ini, kita bisa menggunakan parameter kedua `$emit` untuk mengirimkan nilai:

```html
<button v-on:click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>
```

Lalu kita memantau eventnya dari parent, dan bisa mengakses nilainya dengan `$event`:

```html
<blog-post
  ...
  v-on:enlarge-text="postFontSize += $event"
></blog-post>
```

Atau jika event handlernya adalah sebuah method:

```html
<blog-post
  ...
  v-on:enlarge-text="onEnlargeText"
></blog-post>
```

Nilai tersebut akan menjadi parameter pertama dari method tersebut:

```js
methods: {
  onEnlargeText: function (enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

### Menggunakan `v-model` di Komponen

Custom event juga bisa digunakan untuk membuat `v-model`. Ingat, bahwa:

```html
<input v-model="searchText">
```

adalah sama dengan:

```html
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```

Ketika ingin menggunakannya di komponen, `v-model` akan melakukan:

``` html
<custom-input
  v-bind:value="searchText"
  v-on:input="searchText = $event"
></custom-input>
```

Supaya bisa bekerja, `<input>` di dalam komponen harus:

- Bind atribut `value` attribute ke prop `value`
- Di `input`, kiram custom event `input` dengan nilai baru

Contohnya:

```js
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
})
```

Sekarang `v-model` bisa bekerja di komponen ini:

```html
<custom-input v-model="searchText"></custom-input>
```

Jika kalian ingin mempelajari lebih lanjut tentang custom event di custom komponen, lihat [Custom Events](components-custom-events.html).

## Distribusi Konten dengan Slot

Seperti elemen HTML, kadang penting untuk bisa meneruskan konten ke sebuah komponen, contohnya:

``` html
<alert-box>
  Something bad happened.
</alert-box>
```

Sehingga tampil seperti:

{% raw %}
<div id="slots-demo" class="demo">
  <alert-box>
    Something bad happened.
  </alert-box>
</div>
<script>
Vue.component('alert-box', {
  template: '\
    <div class="demo-alert-box">\
      <strong>Error!</strong>\
      <slot></slot>\
    </div>\
  '
})
new Vue({ el: '#slots-demo' })
</script>
<style>
.demo-alert-box {
  padding: 10px 20px;
  background: #f3beb8;
  border: 1px solid #f09898;
}
</style>
{% endraw %}

Untungya, masalah ini bisa diselesaikan dengan custom element `<slot>`:

```js
Vue.component('alert-box', {
  template: `
    <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>
    </div>
  `
})
```

Di atas, kita cukup menentukan di mana letak slotnya.

Jika kalian ingin mempelajari lebih lanjut tentang slot, baca [Slots](components-slots.html).

## Komponen Dinamis

Kadang, kita butuh secara dinamis berganti-ganti komponen, seperti di tampilan tab:

{% raw %}
<div id="dynamic-component-demo" class="demo">
  <button
    v-for="tab in tabs"
    v-bind:key="tab"
    class="dynamic-component-demo-tab-button"
    v-bind:class="{ 'dynamic-component-demo-tab-button-active': tab === currentTab }"
    v-on:click="currentTab = tab"
  >
    {{ tab }}
  </button>
  <component
    v-bind:is="currentTabComponent"
    class="dynamic-component-demo-tab"
  ></component>
</div>
<script>
Vue.component('tab-home', { template: '<div>Home component</div>' })
Vue.component('tab-posts', { template: '<div>Posts component</div>' })
Vue.component('tab-archive', { template: '<div>Archive component</div>' })
new Vue({
  el: '#dynamic-component-demo',
  data: {
    currentTab: 'Home',
    tabs: ['Home', 'Posts', 'Archive']
  },
  computed: {
    currentTabComponent: function () {
      return 'tab-' + this.currentTab.toLowerCase()
    }
  }
})
</script>
<style>
.dynamic-component-demo-tab-button {
  padding: 6px 10px;
  border-top-left-radius: 3px;
  border-top-right-radius: 3px;
  border: 1px solid #ccc;
  cursor: pointer;
  background: #f0f0f0;
  margin-bottom: -1px;
  margin-right: -1px;
}
.dynamic-component-demo-tab-button:hover {
  background: #e0e0e0;
}
.dynamic-component-demo-tab-button-active {
  background: #e0e0e0;
}
.dynamic-component-demo-tab {
  border: 1px solid #ccc;
  padding: 10px;
}
</style>
{% endraw %}

Kode di atas bisa berjalan dengan menggunakan komponen `<component>` dan atribut khusus `is`:

```html
<!-- Komponen berubah ketika currentTabComponent berubah -->
<component v-bind:is="currentTabComponent"></component>
```

Di contoh di atas, `currentTabComponent` bisa berupa:

- Nama komponen yang terdaftar, atau
- opsi objek komponen

Lihat [fiddle](https://jsfiddle.net/chrisvfritz/o3nycadu/) ini untuk bereksperimen, atau [yang ini](https://jsfiddle.net/chrisvfritz/b2qj69o1/) untuk contoh binding ke objek opsi komponen, daripada nama yang terdaftar.

Jika ingin mempelajari lebih lanjut tentang komponen dinamis, lihat [Komponen Dinamis & Async](components-dynamic-async.html).

## Catatan Penting Penerjaman Templat DOM

Beberapa elemen, seperti `<ul>`, `<ol>`, `<table>` dan `<select>` mempunyai batasan elemen apa saja yang boleh ada di dalam mereka. Dan komponen seperti `<li>`, `<tr>`, dan `<option>` hanya bisa ada di dalam elemen tertentu.

Sehingga bisa terjadi masalah jika seperti di bawah:

``` html
<table>
  <blog-post-row></blog-post-row>
</table>
```

Komponen kustom `<blog-post-row>` akan dianggap konten yang tidak valid, sehingga terjadi error. Untungnya, atribut khusus `is` bisa menyelesaikan masalah ini:

``` html
<table>
  <tr is="blog-post-row"></tr>
</table>
```

Penting, **batasan ini tidak berlaku jika kalian menggunakan**:

- Templat String (e.g. `template: '...'`)
- [Single-file (`.vue`) components](single-file-components.html)
- [`<script type="text/x-template">`](components-edge-cases.html#X-Templates)

Ketika kalian sudah merasa terbiasa dengan informasi di atas, kami merekomendasikan kalian untuk membaca [Komponen Dinamis & Async](components-dynamic-async.html), atau halaman-halaman lain tentang Lebih Dalam Tentang Komponen (Components In-Depth).
