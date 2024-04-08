<template>
  <div>
    <div v-for="movie in movies" :key="movie.id">
      <img :src="movie.poster" alt="Movie Poster">
      <h3>{{ movie.title }}</h3>
      <p>{{ movie.description }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

let movies = ref([]);

const fetchMovies = async () => {
  try {
    const response = await fetch("/api/v1/movies");
    const data = await response.json();
    movies.value = data.movies;
  } catch (error) {
    console.error("Error fetching movies:", error);
  }
};

onMounted(() => {
  fetchMovies();
});
</script>
