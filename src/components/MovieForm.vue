<template>
    <div>
      <!-- Success message -->
      <div v-if="successMessage" class="alert alert-success" role="alert">
        {{ successMessage }}
      </div>
      <!-- Error message -->
      <div v-if="errorMessage" class="alert alert-danger" role="alert">
        {{ errorMessage }}
      </div>
  
      <form id="movieForm" @submit.prevent="saveMovie">
        <div class="form-group mb-3">
          <label for="title" class="form-label">Movie Title</label>
          <input v-model="formData.title" type="text" id="title" name="title" class="form-control" />
        </div>
        <div class="form-group mb-3">
          <label for="description" class="form-label">Description</label>
          <textarea v-model="formData.description" id="description" name="description" class="form-control"></textarea>
        </div>
        <div class="form-group mb-3">
          <label for="poster" class="form-label">Movie Poster</label>
          <input type="file" @change="handleFileUpload" id="poster" class="form-control" />
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
      </form>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from "vue";
  
  let formData = {
    title: '',
    description: '',
    poster: null
  };
  
  let successMessage = ref('');
  let errorMessage = ref('');
  let csrfToken = ref('');
  
  function saveMovie() {
    let movieForm = document.getElementById('movieForm');
    let form_data = new FormData(movieForm);
    fetch("/api/v1/movies", {
      method: 'POST',
      body: form_data,
      headers: {
        'X-CSRFToken': csrfToken.value
      }
    })
    .then(response => {
      if (response.ok) {
        successMessage.value = 'Movie saved successfully.';
        errorMessage.value = ''; // Clear any previous error message
      } else {
        throw new Error('Failed to save movie.');
      }
    })
    .catch(error => {
      errorMessage.value = 'Error: ' + error.message;
      successMessage.value = ''; // Clear any previous success message
    });
  }
  
  function handleFileUpload(event) {
    formData.poster = event.target.files[0];
  }
  
  function getCsrfToken() {
    fetch('/api/v1/csrf-token')
      .then(response => {
        if (!response.ok) {
          throw new Error('Failed to fetch CSRF token');
        }
        return response.json();
      })
      .then(data => {
        console.log(data);
        csrfToken.value = data.csrf_token;
      })
      .catch(error => {
        console.error('Error fetching CSRF token:', error);
      });
  }
  
  onMounted(() => {
    getCsrfToken();
  });
  </script>
  
  <style scoped>
  /* Add any custom styles here */
  </style>
  