---
title: Binding Masukan Formulir
type: guide
order: 10
---

## Penggunaan Dasar

Kalian dapat menggunakan direktif `v-model` untuk membuat data _binding_ dua arah pada masukan elemen formulir, _textarea_, dan _select_. Direktif tersebut akan secara otomatis memilih jalan yang benar untuk memperbarui elemen berdasarkan tipe masukan. Walaupun sedikit terasa ajaib, `v-model` pada dasarnya merupakan _syntax sugar_ untuk pembaruan data pada _event_ masukan pengguna, ditambah perhatian khusus untuk beberapa kasus-kasus tertentu.

<p class="tip">`v-model` akan mengabaikan inisial `value`, attribut-attribut `checked` atau `selected` yang ditemukan pada elemen formulir. Itu akan selalu memperlakukan data instan Vue sebagai sumber kebenaran. Kalian sebaiknya mendeklarasikan nilai inisial pada sisi JavaScript, di dalam opsi `data` pada komponen kalian.</p>

`v-model` secara internal menggunakan properti-properti yang berbeda dan melakukan _emits_ pada _event_ yang berbeda untuk elemen masukan yang berbeda:

- elemen _text_ dan _textarea_ menggunakan properti `value` dan _event_ `input`;
- _checkbox_ dan _radiobutton_ menggunakan properti `checked` dan _event_ `change`;
- masukan _select_ menggunakan `value` sebagai sebuah _prop_ dan `change` sebagai sebuah _event_.

<p class="tip" id="vmodel-ime-tip">Untuk bahasa yang membutuhkan sebuah [IME](https://en.wikipedia.org/wiki/Input_method) (Bahasa Cina, Bahasa Jepang, Bahasa Korea, dll.), kalian akan mengetahui bahwa `v-model` tidak akan diperbarui selama komposisi IME. Jika kalian ingin memenuhi untuk perubahan ini juga, sebaiknya gunakan _event_ `input`</p>


### Text

``` html
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
```

{% raw %}
<div id="example-1" class="demo">
  <input v-model="message" placeholder="edit me">
  <p>Message is: {{ message }}</p>
</div>
<script>
new Vue({
  el: '#example-1',
  data: {
    message: ''
  }
})
</script>
{% endraw %}

### Text Multi Baris

``` html
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```

{% raw %}
<div id="example-textarea" class="demo">
  <span>Multiline message is:</span>
  <p style="white-space: pre-line;">{{ message }}</p>
  <br>
  <textarea v-model="message" placeholder="add multiple lines"></textarea>
</div>
<script>
new Vue({
  el: '#example-textarea',
  data: {
    message: ''
  }
})
</script>
{% endraw %}

{% raw %}
<p class="tip">Interpolasi pada textarea (<code>&lt;textarea&gt;{{text}}&lt;/textarea&gt;</code>) tidak akan berjalan. Untuk itu gunakan <code>v-model</code>.</p>
{% endraw %}

### Checkbox

_Checkbox_ tunggal, nilai _boolean_:

``` html
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
```
{% raw %}
<div id="example-2" class="demo">
  <input type="checkbox" id="checkbox" v-model="checked">
  <label for="checkbox">{{ checked }}</label>
</div>
<script>
new Vue({
  el: '#example-2',
  data: {
    checked: false
  }
})
</script>
{% endraw %}

Lebih dari satu _checkbox_, diikat pada _Array_ yang sama:

``` html
<div id='example-3'>
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Checked names: {{ checkedNames }}</span>
</div>
```

``` js
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []
  }
})
```

{% raw %}
<div id="example-3" class="demo">
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Checked names: {{ checkedNames }}</span>
</div>
<script>
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []
  }
})
</script>
{% endraw %}

### Radio

``` html
<input type="radio" id="one" value="One" v-model="picked">
<label for="one">One</label>
<br>
<input type="radio" id="two" value="Two" v-model="picked">
<label for="two">Two</label>
<br>
<span>Picked: {{ picked }}</span>
```
{% raw %}
<div id="example-4" class="demo">
  <input type="radio" id="one" value="One" v-model="picked">
  <label for="one">One</label>
  <br>
  <input type="radio" id="two" value="Two" v-model="picked">
  <label for="two">Two</label>
  <br>
  <span>Picked: {{ picked }}</span>
</div>
<script>
new Vue({
  el: '#example-4',
  data: {
    picked: ''
  }
})
</script>
{% endraw %}

### Select

_Select_ tunggal:

``` html
<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<span>Selected: {{ selected }}</span>
```
``` js
new Vue({
  el: '...',
  data: {
    selected: ''
  }
})
```
{% raw %}
<div id="example-5" class="demo">
  <select v-model="selected">
    <option disabled value="">Please select one</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-5',
  data: {
    selected: ''
  }
})
</script>
{% endraw %}

<p class="tip">Jika nilai awal dari ekspresi `v-model` kalian tidak cocok dengan semua opsi yang ada, elemen `<select>` akan melakukan _render_ pada _state_ `unselected`. Pada iOS ini akan mengakibatkan pengguna tidak akan dapat memilih item pertama karena iOS tidak mengeksekusi _change event_ pada kasus ini. Maka dari itu direkomendasikan untuk menyediakan opsi yang telah dinonaktifkan dengan nilai kosong, seperti yang didemonstrasikan pada contoh di atas.</p>

Pilihan lebih dari satu (terikat pada _Array_):

``` html
<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<br>
<span>Selected: {{ selected }}</span>
```
{% raw %}
<div id="example-6" class="demo">
  <select v-model="selected" multiple style="width: 50px;">
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <br>
  <span>Selected: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-6',
  data: {
    selected: []
  }
})
</script>
{% endraw %}

Pilihan dinamis yang di-_render_ menggunakan `v-for`:

``` html
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
<span>Selected: {{ selected }}</span>
```
``` js
new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})
```
{% raw %}
<div id="example-7" class="demo">
  <select v-model="selected">
    <option v-for="option in options" v-bind:value="option.value">
      {{ option.text }}
    </option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-7',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})
</script>
{% endraw %}

## Pengikatan Nilai

Untuk _radio_, _checkbox_ dan pilihan _select_, pengikatan nilai `v-model` biasanya berupa _string_ statik (atau _boolean_ untuk _checkbox_):

``` html
<!-- `picked` merupakan string "a" ketika dipilih -->
<input type="radio" v-model="picked" value="a">

<!-- `toggle` bisa berupa true atau false -->
<input type="checkbox" v-model="toggle">

<!-- `selected` merupakan string "abc" ketika opsi pertama dipilih -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

Tapi terkadang kita tidak ingin mengikat nilai kepada properti dinamis pada instan Vue. Kita dapat menggunakan `v-bind` untuk melakukan itu. Sebagai tambahan, menggunakan `v-bind` memberikan kesempatan pada kita untuk mengikat nilai masukan ke nilai _non-string_.

### Checkbox

``` html
<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"
  false-value="no"
>
```

``` js
// ketika dipilih:
vm.toggle === 'yes'
// ketika tidak dipilih:
vm.toggle === 'no'
```

<p class="tip">Attribut `true-value` dan `false-value` tidak mempengaruhi atribut masukan dari `value`, karena peramban tidak menyertakan kotak yang tidak terpilih pada pengiriman formulir. Untuk memastikan bahwa satu dari dua nilai dikirimkan pada sebuah formulir (Contoh "yes" atau "no"), gunakan masukan radio sebagai gantinya.</p>

### Radio

``` html
<input type="radio" v-model="pick" v-bind:value="a">
```

``` js
// ketika dipilih:
vm.pick === vm.a
```

### Select Options

``` html
<select v-model="selected">
  <!-- obyek inline literal -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
```

``` js
// ketika dipilih:
typeof vm.selected // => 'obyek'
vm.selected.number // => 123
```

## Pengubah

### `.lazy`

Secara _default_, `v-model` mensinkronkan masukan dengan data setelah setiap _event_ `input` (dengan pengecualian dari komposisi IME sebagai [yang disebutkan di atas](#vmodel-ime-tip)). Kalian bisa menambahkan pengubah `.lazy` sebagai ganti dari sinkronisasi setelah _event_ `change`:

``` html
<!-- disiknronkan setelah "change" daripada "input" -->
<input v-model.lazy="msg" >
```

### `.number`

Jika kalian ingin masukan pengguna secara otomatis menjadi angka, kalian dapat menambahkan pengubah `number` kepada masukan `v-model`:

``` html
<input v-model.number="age" type="number">
```

Ini sering berguna, karena bahkan dengan `type="number"`, nilai dari elemen masukan HTML selalu mengembalikan sebuah _string_. Jika nilai tidak bisa diurai dengan `parseFloat()`, maka nilai asli yang akan dikembalikan.

### `.trim`

Jika kalian ingin _whitespace_ dari masukan pengguna dipotong secara otomatis, kalian dapat menambahkan pengubah `trim` kepada masukan `v-model`:

```html
<input v-model.trim="msg">
```

## `v-model` dengan Komponen

> Jika kalian belum terbiasa dengan komponen Vue, kalian dapat melewatkan bagian ini untuk saat ini.

Tipe masukan milik HTML tidak akan selalu memenuhi kebutuhan anda. Untungnya, komponen Vue memperbolehkan kalian untuk membangun masukan yang dapat digunakan kembali dengan perilaku yang sepenuhnya dapat disesuaikan. Masukan-masukan ini bahkan bekerja dengan `v-model`!. Untuk mempelajari lebih banyak, baca tentang [Masukan Kustom](components.html#Using-v-model-on-Components) pada panduan Komponen.
