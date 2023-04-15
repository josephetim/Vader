<template>
    <div>
      <header>
        <a href="/">
    <img src="../static/Logo.svg"/>
        </a>
          <div>
            <img id="searchIcon" @click="showSearchbar" class="searchIcon" src="../static/searchIcon.svg" />
      <img id="closeIcon" @click="closeSearchbar" class="closeIcon" src="../static/close-circle.svg" />
      <input id="searchInput" class="searchBar" type="text"/>
        </div>
      </header>
      <div id="hero-page-id" class="hero pb-4" :style="{'background-image': `url(${movie.poster})`}">
        <div class="hero-info d-flex flex-column">
          <!-- <b-breadcrumb id="breadcrumb" :items="items"></b-breadcrumb> -->
          <p class="hero-main-text">{{movie.title}}</p>
          <div class="d-flex flex-row align-items-center stats">
            <div class="d-flex flex-row align-items-center justify-content-between gap-5px">
              <img class="mr-1"  src="../static/star.svg"/>
              <p class="m-0">{{movie.vote_average}}</p>
            </div>
            <button class="rated-age-badge align-items-center"> 16+</button>
          </div>
          <p class="hero-sub-text">{{movie.description}}</p>
          <div class="d-flex flex-row">
            <button class="hero-action-button button-text accentBlue mr-3">Watch now</button>
          </div>
        </div>
      </div>
      <div id="movie-suggestion-id" class="movie-suggestions">
        <p>Casts</p>
        <CastsCard :cardInfo="movie"/>
        <p>You might also like</p>
        <SimilarMoviesCard  :cardInfo="movie"/> 

      </div>
    </div>
  </template>
  
  
  <style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Archivo');
  header{
  background: #1D283C;
  width: 100vw;
  padding: 10px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  flex-wrap: wrap;
}
#searchInput {
  display: block;
  width: 331px;
  height: 40px;
  border: 1px solid #494D61;
  margin: 20px auto;
  padding: 10px 45px;
  background: #494D61  url("../static/searchIcon.svg") no-repeat 15px center;
  background-size: 15px 15px;
  font-size: 16px;
  border: none;
  border-radius: 5px;
  box-shadow: rgba(50, 50, 93, 0.25) 0px 2px 5px -1px,
    rgba(0, 0, 0, 0.3) 0px 1px 3px -1px;
}
#closeIcon {
  display: none;
}

  #breadcrumb {
    background-color: transparent;
  }
  .hero {

    background-size: cover;
    padding-left: 100px;
    padding-top: 150px;
    color: white;
   
  }
  .hero-main-text {

    font-family: 'Archivo';
    font-style: normal;
    font-weight: 600;
    font-size: 42px;
    line-height: 44px;
  }
  .hero-sub-text {
    font-family: 'Archivo';
    font-style: normal;
    font-weight: 400;
    font-size: 16px;
    line-height: 24px;
  }
  .hero-info {
    width: 45vw;
    gap: 15px;
  }
  .button-text {
    font-family: 'Archivo';
    font-style: normal;
    font-weight: 500;
    font-size: 16px;
    line-height: 20px;
    text-align: center;
    color: white;
  }
  .rated-age-badge {
    border: 1px solid #CBD1D8;
    border-radius: 100px;
    width: 49px;
    height: 28px;
    background-color: transparent;
    color: white;
  }
  .stats {
    gap: 20px;
  }
  .hero-action-button {
    justify-content: center;
    align-items: center;
    gap: 4px;
    width: 146px;
    height: 44px;
    border-radius: 6px;
    border-color: transparent;
  }
  .accentBlue {
    background: #3772FF;
  }
.movie-suggestions {
    padding-left: 30px;
    padding-top: 20px;
    background-color: #1B1F32;
}
.movie-suggestions > p {
font-weight: 600;
color: white;
font-size: 20px;
line-height: 28px;
margin-bottom: 0;

}
#searchIcon {
  display: none;
}
@media (max-width: 767px) {
  /* styles for mobile screens */
  #searchInput {
    display: none;
    width: 93vw;
  }
  #searchIcon {
    display: flex;
    justify-content: center;
    position: absolute;
    right: 20px;
    top: 15px;
    transform: scale(1.5);
  }
  #closeIcon {
  display: none;
  position: absolute;
  right: 10px;
  top: 15px;

}
}
  </style>
  
  <script>
  import SimilarMoviesCard from '../components/SimilarMoviesCard.vue'
  import CastsCard from '../components/CastsCard.vue'
  import VueTabs from 'vue-tabs-component';


  export default {
    name: 'MovieDetails',
    data() {
        return {
            movie: {
        id: null,
        title: '',
        poster: '',
        releaseDate: '',
        duration: '',
        description: '',
        vote_average: ''
      }
        }
    },

    async asyncData({ params }) {
  const movieId = params.id;
  console.log(params)
  const response = await fetch(`https://api.themoviedb.org/3/movie/${params.id}?api_key=ddaac02629994be6d82db65948ef5a58`);
  const data = await response.json();
  console.log(data);
  console.log(params)
  console.log(data.release_dates);
  return {
    movie: {
      id: data.id,
      title: data.title,
      poster: `https://image.tmdb.org/t/p/w185${data.poster_path}`,
      releaseDate: new Date(data.release_date).getFullYear(),
      duration: data.runtime,
      description: data.overview,
      vote_average: data.vote_average.toFixed(1),
    }
  };
},
  components: {
    CastsCard,
    SimilarMoviesCard
  },
  computed: {
    movieId() {
        return this.cardInfo.id
    }
  },
  methods:{
    showSearchbar(){
      document.getElementById('searchInput').style.display="block";
      document.getElementById('searchIcon').style.display="none";
      document.getElementById('closeIcon').style.display="block";
      document.getElementById('hero-page-id').style.filter="blur(8px)"
      document.getElementById('movie-suggestion-id').style.filter="blur(8px)"

    },
    closeSearchbar() {
      document.getElementById('searchInput').style.display="none";
      document.getElementById('closeIcon').style.display="none";
      document.getElementById('searchIcon').style.display="block";
      document.getElementById('hero-page-id').style.filter="blur(0px)"
      document.getElementById('movie-suggestion-id').style.filter="blur(0px)"
    }
  }
}

  </script>
  
    