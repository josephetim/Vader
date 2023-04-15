<template>
  <div>
  <header>
    <img src="../static/Logo.svg"/>
    <div>
      <img src="../static/searchIcon.svg"/>
      <input class="searchBar" type="text"/>
    </div>
    
  </header>
  <div class="hero pb-4" :style="{'background-image': `url(${movie.poster})`}">
  <div class="hero-info d-flex flex-column">
    <b-breadcrumb id="breadcrumb" :items="items"></b-breadcrumb>
    <p class="hero-main-text">{{movie.title}}</p>
    <div class="d-flex flex-row align-items-center stats">
    <div class="d-flex flex-row align-items-center justify-content-between gap-5px">
      <img class="mr-1"  src="../static/star.svg"/>
      <p class="m-0">{{vote_average}}</p>
    </div>
    <button class="rated-age-badge align-items-center"> 16+</button>
  </div>
    <p class="hero-sub-text">{{movie.description}}</p>

      <div class="d-flex flex-row">
        <button class="hero-action-button button-text accentBlue mr-3">Watch now</button>
      </div>
    </div>
    </div>
    <div class="movie-suggestions">
      <p>movies for you</p>
      <moviesCard />
    </div>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Archivo');
header{
  background: #1D283C;
  position: fixed;
  width: 100vw;
  padding: 10px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
.searchBar {
  width: 331px;
  height: 40px;
  background: #1B1F32;
  border: 1px solid #494D61;
  border-radius: 6px;
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

</style>

<script>
export default {
  async asyncData({ params }) {
    const response = await fetch(`https://api.themoviedb.org/3/movie/${params.id}?api_key=ddaac02629994be6d82db65948ef5a58`)
    const data = await response.json()
    console.log(data)
    console.log(data.release_dates)
    return { movie: {
      id: data.id,
      title: data.title,
      poster: `https://image.tmdb.org/t/p/w185${data.poster_path}`,
      releaseDate: new Date(data.release_date).getFullYear(),
      duration: data.runtime,
      description: data.overview,
      vote_average: data.vote_average
    } }
  }
}
</script>

  