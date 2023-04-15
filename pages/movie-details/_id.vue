 <template>
    <div class="movie-details">
      <div class="movie-info">
        <h2>{{ movie.title }}</h2>
        <img :src="movie.poster" :alt="movie.title" />
        <p>Release Year: {{ movie.releaseYear }}</p>
        <p>Duration: {{ movie.duration }} minutes</p>
        <p>Overview: {{ movie.overview }}</p>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    async asyncData({ params }) {
      const response = await fetch(
        `https://api.themoviedb.org/3/movie/${params.id}?api_key=ddaac02629994be6d82db65948ef5a58`
      );
      const data = await response.json();
      const movie = {
        id: data.id,
        title: data.title,
        poster: `https://image.tmdb.org/t/p/w185${data.poster_path}`,
        releaseYear: new Date(data.release_date).getFullYear(),
        duration: data.runtime,
        overview: data.overview,
      };
      return { movie };
    },
  };
  </script>
  
  <style scoped>
  .movie-details {
    display: flex;
    justify-content: center;
  }
  
  .movie-info {
    max-width: 600px;
    margin: 2rem;
  }
  
  .movie-info h2 {
    margin-bottom: 1rem;
  }
  
  .movie-info img {
    width: 100%;
    margin-bottom: 1rem;
  }
  
  .movie-info p {
    margin-bottom: 1rem;
  }
  </style> 
  <!-- <template>
    <movieDetails />
  </template>
  
  <script>
  import movieDetails from '../../components/movieDetails.vue'
  
  export default {
    components: {
      movieDetails
    }
  }
  </script> -->