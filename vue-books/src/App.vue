<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { useBook } from './composables/useBook'
import BookForm from './components/BookForm.vue'
import type { Book } from './models/book'

const { books, loading, getBooks, createBook, updateBook, deleteBook } = useBook()

const showForm = ref(false)
const selectedBook = ref<Book | null>(null)

onMounted(() => {
  getBooks()
})

const openCreate = () => {
  selectedBook.value = null
  showForm.value = true
}

const openEdit = (book: Book) => {
  selectedBook.value = book
  showForm.value = true
}

const saveBook = async (data: Book) => {
  if (selectedBook.value?.id) {
    await updateBook(selectedBook.value.id, data)
  } else {
    await createBook(data)
  }
  showForm.value = false
}

const removeBook = async (id: number) => {
  if (confirm('Yakin hapus data?')) {
    await deleteBook(id)
    await getBooks()
  }
}

</script>

<template>
  <div class="container">
    <h1>Books</h1>

    <button class="btn-create" @click="openCreate">+ Create Book</button>

    <p v-if="loading">Loading...</p>

    <table v-else border="1" cellpadding="8">
      <thead>
        <tr>
          <th>ID</th>
          <th>Title</th>
          <th>Author</th>
          <th>Year</th>
          <th>Description</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="book in books" :key="book.id">
          <td>{{ book.id }}</td>
          <td>{{ book.title }}</td>
          <td>{{ book.author }}</td>
          <td>{{ book.year }}</td>
          <td>{{ book.description }}</td>
          <td>
            <button class="btn-edit" @click="openEdit(book)">Edit</button>
            <button class="btn-remove" @click="removeBook(book.id!)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>

    <BookForm 
      :show="showForm" 
      :book="selectedBook" 
      @close="showForm = false" 
      @save="saveBook" 
    />
  </div>
</template>


<!-- CSS -->

<style scoped>
/* Reset dasar untuk memastikan tampilan konsisten */
* {
  box-sizing: border-collapse;
  margin: 0;
  padding: 0;
}

/* Container Utama */
div {
  background-color: lightslategrey; /* Abu-abu sangat gelap, lembut di mata */
  color: blue;            /* Teks putih gading */
  padding: 60px;             /* Padding luar lebih lega */
  min-height: 100vh;
  font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; /* Font modern */
}

/* Judul H1 */
h1 {
  font-size: 2.5rem;
  font-weight: 700;
  margin-bottom: 30px;
  color: #ffffff;
  border-bottom: 2px solid #333; /* Garis dekoratif di bawah judul */
  padding-bottom: 10px;
  display: inline-block;
}

/* Pesan Loading */
p[v-if="loading"] {
  font-style: italic;
  color: #888;
  margin-top: 20px;
}

/* --- Styling Tabel Estetik --- */
table {
  width: 100%;
  border-collapse: collapse; /* Wajib: agar garis tipis antar baris menyatu */
  margin-top: 10px;
  background-color: #1e1e1e; /* Warna background tabel sedikit lebih terang dari container */
  border-radius: 8px;        /* Sudut tabel melengkung */
  overflow: hidden;          /* Memastikan konten tidak keluar dari sudut melengkung */
  box-shadow: 0 4px 15px rgba(0,0,0,0.5); /* Bayangan lembut agar tabel terlihat "mengambang" */
}

/* Styling Header Tabel (Thead) */
thead {
  background-color: #2d2d2d; /* Warna header lebih tegas */
}

th {
  text-align: left;
  padding: 18px 15px;       /* Padding vertikal lebih lega agar header terlihat empuk */
  font-size: 0.9rem;
  text-transform: uppercase; /* Membuat teks header kapital */
  letter-spacing: 1px;       /* Memberi jarak antar huruf */
  font-weight: 700;
  color: #aaaaaa;
  border-bottom: 2px solid #444; /* Garis bawah header lebih tebal */
}

/* Styling Body Tabel (Tbody) */
tbody tr {
  border-bottom: 1px solid #333; /* Garis tipis halus hanya di bawah setiap baris */
  transition: background-color 0.3s ease; /* Transisi halus untuk efek hover */
}

/* Hapus garis bawah pada baris terakhir agar rapi */
tbody tr:last-child {
  border-bottom: none;
}

td {
  padding: 18px 15px;       /* Padding sama dengan header agar sejajar sempurna */
  font-size: 1rem;
  color: #cccccc;
  vertical-align: top;       /* Teks description panjang akan rapi di atas */
  line-height: 1.5;         /* Jarak antar baris teks description */
}

/* Kolom Description: Beri perhatian khusus karena teksnya panjang */
td:last-child {
  max-width: 400px;         /* Batasi lebar maksimal agar tidak merusak layout */
  color: #aaaaaa;           /* Warna teks description sedikit lebih redup */
}

/* --- Efek Hover --- */
/* Memberi warna background berbeda saat mouse di atas baris */
tbody tr:hover {
  background-color: #2a2a2a;
  cursor: pointer;          /* Mengubah cursor menjadi jari tunjuk */
}

/* Tombol Create */
.btn-create {
  padding: 10px 20px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-bottom: 20px;
  margin-left: 35px;
}

/* Tombol Edit */
.btn-edit {
  padding: 6px 12px;
  background-color: #2196F3;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn-remove {
  padding: 6px 12px;
  background-color: red;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-left: 7px;
}

.btn-edit:hover { background-color: #1976D2; }
</style>