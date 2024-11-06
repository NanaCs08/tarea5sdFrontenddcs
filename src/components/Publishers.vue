<template>
  <div class="container mt-4">
    <h2 class="text-center mb-4">Publishers</h2>
    <ul class="list-group mb-3">
      <li v-for="publisher in publishers" :key="publisher.id" class="list-group-item d-flex justify-content-between align-items-center">
        <div>
          <h5 class="mb-0">{{ publisher.publisher }}</h5>
          <small class="text-muted">{{ publisher.country }} - Founded: {{ publisher.founded }}</small>
        </div>
        <div>
          <button class="btn btn-outline-primary btn-sm me-2" @click="setEditPublisher(publisher)">Edit</button>
          <button class="btn btn-outline-danger btn-sm" @click="deletePublisher(publisher.id)">Delete</button>
        </div>
      </li>
    </ul>
    <button class="btn btn-success mb-3" @click="toggleAddForm">{{ showAddForm ? 'Cancel' : 'Add Publisher' }}</button>

    <!-- Form for adding or editing a publisher -->
    <div v-if="showAddForm" class="card p-3 mt-3">
      <h4 class="card-title text-center">{{ editingPublisher ? 'Edit Publisher' : 'Add Publisher' }}</h4>
      <form @submit.prevent="editingPublisher ? updatePublisher() : addPublisher()">
        <div class="mb-3">
          <label for="publisher" class="form-label">Publisher</label>
          <input type="text" id="publisher" class="form-control" v-model="publisherForm.publisher" required>
        </div>
        <div class="mb-3">
          <label for="country" class="form-label">Country</label>
          <input type="text" id="country" class="form-control" v-model="publisherForm.country" required>
        </div>
        <div class="mb-3">
          <label for="founded" class="form-label">Founded Year</label>
          <input type="number" id="founded" class="form-control" v-model="publisherForm.founded" required>
        </div>
        <div class="mb-3">
          <label for="genre" class="form-label">Genre</label>
          <input type="text" id="genre" class="form-control" v-model="publisherForm.genre" required>
        </div>
        <button type="submit" class="btn btn-primary w-100">{{ editingPublisher ? 'Update' : 'Add' }} Publisher</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'PublisherList',
  data() {
    return {
      publishers: [],
      publisherForm: {
        id: null,
        publisher: '',
        country: '',
        founded: '',
        genre: ''
      },
      editingPublisher: false,
      showAddForm: false
    };
  },
  methods: {
    async fetchPublishers() {
      try {
        const response = await axios.get('https://tarea5sddcsbackend.netlify.app/.netlify/functions/publishers', {
          headers: {
            'X-Password': localStorage.getItem('authPassword') 
          }
        });
        this.publishers = response.data;
      } catch (error) {
        console.error("Error fetching publishers:", error);
      }
    },
    async addPublisher() {
      try {
        const response = await axios.post('https://tarea5sddcsbackend.netlify.app/.netlify/functions/publishers', this.publisherForm, {
          headers: {
            'X-Password': localStorage.getItem('authPassword') 
          }
        });
        this.publishers.push(response.data);
        this.clearForm();
      } catch (error) {
        console.error("Error adding publisher:", error);
      }
    },
    async deletePublisher(id) {
      try {
        await axios.delete('https://tarea5sddcsbackend.netlify.app/.netlify/functions/publishers', {
          headers: {
            'X-Password': localStorage.getItem('authPassword'), 
            'Content-Type': 'application/json'
          },
          data: { id }
        });
        this.publishers = this.publishers.filter(publisher => publisher.id !== id);
      } catch (error) {
        console.error("Error deleting publisher:", error);
      }
    },
    setEditPublisher(publisher) {
      this.publisherForm = { ...publisher };
      this.editingPublisher = true;
      this.showAddForm = true;
    },
    async updatePublisher() {
      try {
        await axios.put('https://tarea5sddcsbackend.netlify.app/.netlify/functions/publishers', this.publisherForm, {
          headers: {
            'X-Password': localStorage.getItem('authPassword') 
          }
        });
        const index = this.publishers.findIndex(publisher => publisher.id === this.publisherForm.id);
        if (index !== -1) this.publishers.splice(index, 1, this.publisherForm);
        this.clearForm();
      } catch (error) {
        console.error("Error updating publisher:", error);
      }
    },
    clearForm() {
      this.publisherForm = {
        id: null,
        publisher: '',
        country: '',
        founded: '',
        genre: ''
      };
      this.editingPublisher = false;
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
    this.fetchPublishers();
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
