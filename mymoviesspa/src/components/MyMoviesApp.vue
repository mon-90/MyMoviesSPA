<template>
  <div id="app">
    <h1 class="heading">Moje filmy</h1>
    <button class="button addMovieBtn" @click="addMovie" :disabled="modalIsOpen">Dodaj film</button>
    <table>
      <tr>
        <th>Lp.</th>
        <th>Tytuł filmu</th>
        <th class="productionYear">Rok produkcji</th>
        <th class="tableActions"></th>
      </tr>
      <tr v-for="movie in movies" :key="movie.id">
        <td>{{ movies.indexOf(movie) + 1 }}</td>
        <td>{{ movie.title }}</td>
        <td>{{ movie.productionYear }}</td>
        <td>
          <button class="button smallBtn show" @click="showMovie(movie.id)" :disabled="modalIsOpen">Podgląd</button> |
          <button class="button smallBtn edit" @click="editMovie(movie.id)" :movieId="movie.id" :disabled="modalIsOpen">Edytuj</button> |
          <button class="button smallBtn remove" @click="removeMovie(movie.id)" :disabled="modalIsOpen">Usuń</button>
        </td>
      </tr>
    </table>
    <MovieModal v-if="showMovieModal === true" @open="modalToggleState" :actionName="actionName" :movieId="movieId" :movie="movie"/>
  </div>
</template>

<script>
import MovieModal from './MovieModal.vue'
import axios from 'axios'

export default {
  name: "MyMoviesApp",
  components: {
    MovieModal
  },

  data() {
    return {
      showMovieModal: false,
      actionName: '',
      modalIsOpen: false,
      movieId: null,
      movies: [],
      movie: {}
    } 
  },

  created() {
    axios.get("https://localhost:44353/api/movie")
      .then(response => this.movies = response.data);
  },

  methods: {
    addMovie() {
      this.actionName = "addMovie";
      this.showMovieModal = true;
      this.modalIsOpen = true;
    },
    removeMovie: async function (id) {
      if (confirm("Czy na pewno chcesz usunąć ten film ze swojej kolekcji?")) {
        await axios.delete(`https://localhost:44353/api/movie/${id}`);
      }
      axios.get("https://localhost:44353/api/movie")
        .then(response => this.movies = response.data);
    },
    editMovie: async function (id) {
      this.actionName = "editMovie";
      this.showMovieModal = true;
      this.modalIsOpen = true;
      await axios.get(`https://localhost:44353/api/movie/${id}`)
        .then(response => this.movie = response.data);
    },
    showMovie: async function (id) {
      this.actionName = "showMovie";
      this.showMovieModal = true;
      this.modalIsOpen = true;
      await axios.get(`https://localhost:44353/api/movie/${id}`)
        .then(response => {
          this.movie = response.data;
        });
    },
    modalToggleState(value) {
      this.modalIsOpen = value;
      this.showMovieModal = false;
    }
  }
}
</script>

<style scoped>

* {
  margin: 0;
  padding: 0;
}

#app {
  position: relative;
  width: 50vw;
  margin: 0 auto;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
}

.heading {
  display: inline-block;
  padding-left: 3rem;
  padding-top: 1rem;
  padding-bottom: 1.2rem;
  font-weight: 300;
  font-size: 3rem;
  text-transform: uppercase;
  letter-spacing: 3px;
}

.button {
  cursor: pointer;
  border: none;
}

.tableActions {
  width: 35%;
}

.productionYear {
  width: 25%;
}

.addMovieBtn {
  float: right;
  width: 7rem;
  height: 3rem;
  margin-top: 2rem;
  margin-right: 3rem;
  background-color: rgb(18, 161, 30);
  border-radius: 5px;
  color: rgb(235, 239, 241);
  font-weight: 600;
  font-size: 0.9rem;
}

.smallBtn {
  padding-left: 0.5rem;
  padding-right: 0.5rem;
  border-radius: 5px;
  font-weight: 600;
}

.show {
  color: #17a2b8; 
}

.edit {
  color: #007bff;
}

.remove {
  color: #dc3545;
}

table {
  width: 100%;
}

th, td {
  border-bottom: 1px solid #ddd;
}

th {
  text-align: left;
}

td {
  height: 2rem;
}

</style>
