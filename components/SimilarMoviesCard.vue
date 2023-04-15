<template>

    <div class="movies-carousel">
        <div class="navigation">
            <img id="left-arrow-grey"  src="../static/arrow-left-grey.svg" class="arrow left" @click="scroll('left')">
            <img id="right-arrow-light" src="../static/arrow-right.svg" class="arrow right" @click="scroll('right')">
            <img id="left-arrow-light" src="../static/arrow-left.svg" class="arrow left"  @click="scroll('left')">
            <img id="right-arrow-grey" src="../static/arrow-right-grey.svg" class="arrow right"  @click="scroll('right')"> 
        </div>
      <div class="movies" ref="movies">
        <div class="movie-card" v-for="movie in movies" :key="movie.id">  
          <a :href="`/movie-details/${movie.id}`"> 
            <img :src="movie.poster" :alt="movie.title" />
            <div class="movie-info">
              <h3 class="movie-title">{{ movie.title }}</h3>
              <p class="movie-sec-info">{{ movie.releaseDate }} | {{ movie.duration }} minutes</p>
              
            </div>
          </a> 
        </div>
      </div>

    </div>
  </template>


  <script>

  export default {
    name: 'SimilarMoviesCard',
    data() {
      return {
        movies: [],
        movieWidth: 0,
        movieMargin: 0,
        moviesPerScreen: 0,
        currentOffset: 0,
        maxOffset: 0,
      }
    },
    async mounted() {
      const response = await fetch(`https://api.themoviedb.org/3/movie/${this.cardInfo.id}/similar?api_key=ddaac02629994be6d82db65948ef5a58`)
      const data = await response.json()
      this.movies = data.results.map( movie => ({
        id: movie.id,
        title: movie.title,
        poster: `https://image.tmdb.org/t/p/w185${movie.poster_path}`,
        releaseDate: new Date(movie.release_date).getFullYear(),
        duration: 0
       
      }))
         // fetch the duration of each movie using the TMDb API
    for (const movie of this.movies) {
      const response = await fetch(`https://api.themoviedb.org/3/movie/${movie.id}?api_key=ddaac02629994be6d82db65948ef5a58`)
      const data = await response.json()
      movie.duration = `${Math.floor(data.runtime/60)}h ${data.runtime%60}m` // set the duration of the movie
    }
      this.$nextTick(() => {
        this.initCarousel()
      })
    },
    methods: {
      initCarousel() {
        const moviesEl = this.$refs.movies
        const movieCardEl = moviesEl.querySelector('.movie-card')
        this.movieWidth = movieCardEl.offsetWidth
        this.movieMargin = parseInt(getComputedStyle(movieCardEl).marginRight)
        this.moviesPerScreen = Math.floor(moviesEl.offsetWidth / (this.movieWidth + this.movieMargin))
        this.maxOffset = (this.movies.length - this.moviesPerScreen) * (this.movieWidth + this.movieMargin)
          // Add touch event listeners
           let touchStartX = 0
   let touchEndX = 0
   let touchStartTime = 0
   let touchEndTime = 0
   moviesEl.addEventListener('touchstart', event => {
     touchStartX = event.touches[0].clientX
     touchStartTime = Date.now()
   })
   moviesEl.addEventListener('touchend', event => {
     touchEndX = event.changedTouches[0].clientX
     touchEndTime = Date.now()
     const touchDuration = touchEndTime - touchStartTime
     const touchDistance = touchEndX - touchStartX
     const swipeSpeed = Math.abs(touchDistance) / touchDuration  //pixels per millisecond
     const pixelsToMove = Math.round(swipeSpeed * 100) // adjust the coefficient to control the sensitivity
     if (touchEndX > touchStartX) {
       this.scroll('right', pixelsToMove)
     } else if (touchEndX < touchStartX) {
       this.scroll('left', pixelsToMove)
     }
   })
      },
      leftScrollImage() {
  if (this.currentOffset === 0) {
    document.getElementById("left-arrow-grey").style.display = "flex";
    document.getElementById("left-arrow-light").style.display = "none";
  } else {
    document.getElementById("left-arrow-grey").style.display = "none";
    document.getElementById("left-arrow-light").style.display = "flex";
  }
},
rightScrollImage() {
  if (this.currentOffset === -this.maxOffset) {
    document.getElementById("right-arrow-grey").style.display = "flex";
    document.getElementById("right-arrow-light").style.display = "none";
  } else {
    document.getElementById("right-arrow-grey").style.display = "none";
    document.getElementById("right-arrow-light").style.display = "flex";
  }
},
scroll(direction) {
        const delta = direction === 'left' ? this.movieWidth + this.movieMargin : -(this.movieWidth + this.movieMargin)
        const newOffset = this.currentOffset + delta
        if (newOffset > 0) {
          this.currentOffset = 0
          this.leftScrollImage()
        } else if (newOffset < -this.maxOffset) {
          this.currentOffset = -this.maxOffset
          this.rightScrollImage()
        } else {
          this.currentOffset = newOffset
          if (direction === 'right') {
            this.leftScrollImage()
        } else if (direction === 'left') {
            this.rightScrollImage()
        }
        }

        this.$refs.movies.style.transform = `translateX(${this.currentOffset}px)`;

      }
    },
    props: {
        cardInfo: {
            type: Object,
            required: true,
        },
    },
  }
  </script>
  
  <style scoped>
  .movies-carousel {
    position: relative;
    overflow: hidden;
  }
  
  .movies {
    display: flex;
    flex-wrap: nowrap;
    transition: transform 0.5s ease-in-out;
  }
  
  .movie-card {
  flex: 0 0 199px; /* Set a fixed width for each card */
  margin-right: 1rem; /* Add some space between cards */
  box-shadow: 0 0.25rem 0.5rem rgba(0, 0, 0, 0.1); /* Add a subtle box shadow */
  background-color: #1B1F32; /* Set a white background color */
}
  
  .movie-card img {
    width: 100%;
  }
  
  .movie-info {
    background-color: #1B1F32;
  }
  .movie-title {
    font-family: 'Archivo';
    font-style: normal;
    font-weight: 600;
    font-size: 16px;
    line-height: 24px;
    color: #FFFFFF;
  }
  .movie-sec-info {
    font-family: 'Archivo';
    font-style: normal;
    font-weight: 400;
    font-size: 12px;
    line-height: 20px;
    color: #E0E3E8;
  }
  #left-arrow-light {
    display: none;
  }
  #right-arrow-grey {
    display: none;
  }
  .navigation {
    position: relative;
  top: 10px;
  display: flex;
  gap: 10px;
  justify-content: flex-end;
  margin-right: 20px;
  margin-bottom: 20px;
  margin-left: 90%;
  height: 40px;

  }
  .arrow {
  
  top: 50%;

  width: 2rem;
  height: 2rem;

  cursor: pointer;
}

.arrow.left {
    position: absolute;
right: 40px;
top: 0px;
}

.arrow.right {
    position: absolute;
  right: 0;
  top: 0px;
}

.arrow::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%) rotate(45deg);
  width: 1rem;
  height: 1rem;
  border-style: solid;
  border-width: 0.25rem 0.25rem 0 0;
  border-color: #fff;
}

.arrow.left::before {
  transform: translate(-50%, -50%) rotate(-135deg);
}

.arrow.right::before {
  transform: translate(-50%, -50%) rotate(45deg);
}
</style>  