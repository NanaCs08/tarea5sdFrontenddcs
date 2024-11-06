<template>
  <div class="container mt-4">
    <h2 class="text-center mb-4">Authors</h2>
    <ul class="list-group mb-3">
      <li v-for="author in authors" :key="author._id" class="list-group-item d-flex justify-content-between align-items-center">
        <div>
          <h5 class="mb-0">{{ author.author }}</h5>
          <small class="text-muted">{{ author.nationality }} - {{ author.fields }}</small>
        </div>
        <div>
          <button class="btn btn-outline-primary btn-sm me-2" @click="setEditAuthor(author)">Edit</button>
          <button class="btn btn-outline-danger btn-sm" @click="deleteAuthor(author._id)">Delete</button>
        </div>
      </li>
    </ul>
    <button class="btn btn-success mb-3" @click="toggleAddForm">{{ showAddForm ? 'Cancel' : 'Add Author' }}</button>

    <div v-if="showAddForm" class="card p-3 mt-3">
      <h4 class="card-title text-center">{{ editingAuthor ? 'Edit Author' : 'Add Author' }}</h4>
      <form @submit.prevent="editingAuthor ? updateAuthor() : addAuthor()">
        <div class="form-group mb-3">
          <label for="author">Author</label>
          <input type="text" id="author" class="form-control" v-model="authorForm.author" required>
        </div>
        <div class="form-group mb-3">
          <label for="nationality">Nationality</label>
          <input type="text" id="nationality" class="form-control" v-model="authorForm.nationality" required>
        </div>
        <div class="form-group mb-3">
          <label for="birth_year">Birth Year</label>
          <input type="number" id="birth_year" class="form-control" v-model="authorForm.birth_year" required>
        </div>
        <div class="form-group mb-3">
          <label for="fields">Fields</label>
          <input type="text" id="fields" class="form-control" v-model="authorForm.fields" required>
        </div>
        <button type="submit" class="btn btn-primary w-100">{{ editingAuthor ? 'Update' : 'Add' }} Author</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'AuthorList',
  data() {
    return {
      authors: [],
      authorForm: {
        id: null,
        author: '',
        nationality: '',
        birth_year: '',
        fields: ''
      },
      editingAuthor: false,
      showAddForm: false
    };
  },
  methods: {
    async fetchAuthors() {
      try {
        const response = await axios.get('https://tarea5sddcsbackend.netlify.app/.netlify/functions/authors', {
          headers: {
            'X-Password': localStorage.getItem('authPassword') 
          }
        });
        this.authors = response.data;
      } catch (error) {
        console.error("Error fetching authors:", error);
      }
    },
    async addAuthor() {
      try {
        const response = await axios.post('https://tarea5sddcsbackend.netlify.app/.netlify/functions/authors', this.authorForm, {
          headers: {
            'X-Password': localStorage.getItem('authPassword') 
          }
        });
        this.authors.push(response.data);
        this.clearForm();
      } catch (error) {
        console.error("Error adding author:", error);
      }
    },
    async deleteAuthor(id) {
      try {
        await axios.delete('https://tarea5sddcsbackend.netlify.app/.netlify/functions/authors', {
          headers: {
            'X-Password': localStorage.getItem('authPassword'), 
            'Content-Type': 'application/json'
          },
          data: { id }
        });
        this.authors = this.authors.filter(author => author._id !== id);
      } catch (error) {
        console.error("Error deleting author:", error);
      }
    },
    setEditAuthor(author) {
      this.authorForm = { ...author };
      this.editingAuthor = true;
      this.showAddForm = true;
    },
    async updateAuthor() {
      try {
        await axios.put('https://tarea5sddcsbackend.netlify.app/.netlify/functions/authors', this.authorForm, {
          headers: {
            'X-Password': localStorage.getItem('authPassword') 
          }
        });
        const index = this.authors.findIndex(author => author._id === this.authorForm._id);
        if (index !== -1) this.authors.splice(index, 1, this.authorForm);
        this.clearForm();
      } catch (error) {
        console.error("Error updating author:", error);
      }
    },
    clearForm() {
      this.authorForm = {
        id: null,
        author: '',
        nationality: '',
        birth_year: '',
        fields: ''
      };
      this.editingAuthor = false;
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
    this.fetchAuthors();
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
