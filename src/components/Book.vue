<template>
  <div class="container mt-4">
    <h2 class="text-center mb-4">Books</h2>
    <ul class="list-group mb-3">
      <li v-for="book in books" :key="book.id" class="list-group-item d-flex justify-content-between align-items-center">
        <div>
          <h5 class="mb-0">{{ book.title }}</h5>
          <small class="text-muted">{{ book.author }} - {{ book.language }}</small>
        </div>
        <div>
          <button class="btn btn-outline-primary btn-sm me-2" @click="setEditBook(book)">Edit</button>
          <button class="btn btn-outline-danger btn-sm" @click="deleteBook(book.id)">Delete</button>
        </div>
      </li>
    </ul>
    <button class="btn btn-success mb-3" @click="toggleAddForm">{{ showAddForm ? 'Cancel' : 'Add Book' }}</button>

    <!-- Form for adding or editing a book -->
    <div v-if="showAddForm" class="card p-3 mt-3">
      <h4 class="card-title text-center">{{ editingBook ? 'Edit Book' : 'Add Book' }}</h4>
      <form @submit.prevent="editingBook ? updateBook() : addBook()">
        <div class="mb-3">
          <label for="title" class="form-label">Title</label>
          <input type="text" id="title" class="form-control" v-model="bookForm.title" required>
        </div>
        <div class="mb-3">
          <label for="edition" class="form-label">Edition</label>
          <input type="text" id="edition" class="form-control" v-model="bookForm.edition" required>
        </div>
        <div class="mb-3">
          <label for="copyright" class="form-label">Copyright Year</label>
          <input type="number" id="copyright" class="form-control" v-model="bookForm.copyright" required>
        </div>
        <div class="mb-3">
          <label for="language" class="form-label">Language</label>
          <input type="text" id="language" class="form-control" v-model="bookForm.language" required>
        </div>
        <div class="mb-3">
          <label for="pages" class="form-label">Pages</label>
          <input type="number" id="pages" class="form-control" v-model="bookForm.pages" required>
        </div>
        <div class="mb-3">
          <label for="author" class="form-label">Author</label>
          <input type="text" id="author" class="form-control" v-model="bookForm.author" required>
        </div>
        <button type="submit" class="btn btn-primary w-100">{{ editingBook ? 'Update' : 'Add' }} Book</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'BookList',
  data() {
    return {
      books: [],
      bookForm: {
        id: null,
        title: '',
        edition: '',
        copyright: '',
        language: '',
        pages: '',
        author: '',
        author_id: '',
        publisher: '',
        publisher_id: ''
      },
      editingBook: false,
      showAddForm: false
    };
  },
  methods: {
    async fetchBooks() {
      try {
        const response = await axios.get('https://tarea5sddcsbackend.netlify.app/.netlify/functions/books', {
          headers: {
            'X-Password': localStorage.getItem('authPassword') 
          }
        });
        this.books = response.data;
      } catch (error) {
        console.error("Error fetching books:", error);
      }
    },
    async addBook() {
      try {
        const response = await axios.post('https://tarea5sddcsbackend.netlify.app/.netlify/functions/books', this.bookForm, {
          headers: {
            'X-Password': localStorage.getItem('authPassword') 
          }
        });
        this.books.push(response.data);
        this.clearForm();
      } catch (error) {
        console.error("Error adding book:", error);
      }
    },
    async deleteBook(id) {
      try {
        await axios.delete('https://tarea5sddcsbackend.netlify.app/.netlify/functions/books', {
          headers: {
            'X-Password': localStorage.getItem('authPassword'), 
            'Content-Type': 'application/json'
          },
          data: { id }
        });
        this.books = this.books.filter(book => book.id !== id);
      } catch (error) {
        console.error("Error deleting book:", error);
      }
    },
    setEditBook(book) {
      this.bookForm = { ...book };
      this.editingBook = true;
      this.showAddForm = true;
    },
    async updateBook() {
      try {
        const response = await axios.put('https://tarea5sddcsbackend.netlify.app/.netlify/functions/books', this.bookForm, {
          headers: {
            'X-Password': localStorage.getItem('authPassword') 
          }
        });
        const index = this.books.findIndex(book => book.id === this.bookForm.id);
        if (index !== -1) this.books.splice(index, 1, response.data);
        this.clearForm();
      } catch (error) {
        console.error("Error updating book:", error);
      }
    },
    clearForm() {
      this.bookForm = {
        id: null,
        title: '',
        edition: '',
        copyright: '',
        language: '',
        pages: '',
        author: '',
        author_id: '',
        publisher: '',
        publisher_id: ''
      };
      this.editingBook = false;
      this.showAddForm = false;
    },
    toggleAddForm() {
      this.showAddForm = !this.showAddForm;
      if (!this.showAddForm) {
        this.clearForm();
      }
    }
  },
  mounted() {
    this.fetchBooks();
  }
};
</script>

<style scoped>
.container {
  max-width: 600px;
}
.card {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}
.btn-edit, .btn-delete {
  margin-left: 5px;
}
</style>
