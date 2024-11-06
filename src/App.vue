<template>
  <div id="app">
    <div v-if="isAuthenticated">
      <nav>
        <router-link to="/authors">Authors</router-link>
        <router-link to="/publishers">Publishers</router-link>
        <router-link to="/books">Books</router-link>
        <button class="btn-logout" @click="logout">Logout</button>
      </nav>
      <router-view />
    </div>
    <div v-else class="auth-modal">
      <h2>Enter Password</h2>
      <input type="password" v-model="password" placeholder="Enter password">
      <button @click="authenticate">Submit</button>
      <p v-if="authError" class="error">Incorrect password. Please try again.</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      isAuthenticated: false,
      password: '',
      authError: false
    };
  },
  methods: {
    authenticate() {
      if (this.password === 'daniacs') { // Cambia esto por la contraseña correcta
        localStorage.setItem('authPassword', this.password);
        this.isAuthenticated = true;
        this.authError = false;
      } else {
        this.authError = true;
      }
    },
    logout() {
      localStorage.removeItem('authPassword'); // Elimina la clave de autenticación
      this.isAuthenticated = false; // Oculta el contenido y muestra el formulario de autenticación
      this.password = ''; // Limpia el campo de la contraseña
    }
  },
  mounted() {
    const savedPassword = localStorage.getItem('authPassword');
    if (savedPassword === 'daniacs') { // Cambia esto por la contraseña correcta
      this.isAuthenticated = true;
    }
  }
};
</script>

<style>
nav {
  margin: 20px;
}
nav a {
  margin: 0 10px;
  text-decoration: none;
  color: #42b983;
}
nav a.router-link-exact-active {
  font-weight: bold;
  color: #35495e;
}
button {
  margin-left: 10px;
  padding: 5px 10px;
  font-size: 14px;
  cursor: pointer;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  transition: background-color 0.3s;
}
button:hover {
  background-color: #1d8a70;
}
</style>
