<template>

    <div class="casts-carousel">
        <div class="navigation">
            <img id="left-arrow-grey"  src="../static/arrow-left-grey.svg" class="arrow left" @click="scroll('left')">
            <img id="right-arrow-light" src="../static/arrow-right.svg" class="arrow right" @click="scroll('right')">
            <img id="left-arrow-light" src="../static/arrow-left.svg" class="arrow left"  @click="scroll('left')">
            <img id="right-arrow-grey" src="../static/arrow-right-grey.svg" class="arrow right"  @click="scroll('right')"> 
        </div>
      <div class="casts" ref="casts">
        <div class="cast-card" v-for="cast in casts" :key="cast.id">  
         
            <img :src="cast.castImage" :alt="cast.castName" />
            <div class="cast-info">
              <h3 class="cast-name-font-style">{{ cast.castName }}</h3>
              <p class="cast-subinfo-font-style">{{ cast.characterPlayed }}</p>
            </div>
          
        </div>
      </div>

    </div>
  </template>


  <script>

  export default {
    name: 'CastsCard',
    data() {
      return {
        casts: [],
        castWidth: 0,
        castMargin: 0,
        castsPerScreen: 0,
        currentOffset: 0,
        maxOffset: 0,
        


      }
    },
    async mounted() {
        
    console.log('cardInfo:', this.cardInfo.id);
    const apiKey = "ddaac02629994be6d82db65948ef5a58"
        const response = await fetch(`https://api.themoviedb.org/3/movie/${this.cardInfo.id}/credits?api_key=${apiKey}`);
       const data = await response.json()
       this.casts = data.cast.map(cast => ({
         id: cast.id,
         castName: cast.name,
         castImage: `https://image.tmdb.org/t/p/w185${cast.profile_path}`,
         characterPlayed: cast.character,
       }))
       this.$nextTick(() => {
         this.initCarousel()
       })
    },
    methods: {
      initCarousel() {
        const castsEl = this.$refs.casts
        const castCardEl = castsEl.querySelector('.cast-card')
        this.castWidth = castCardEl.offsetWidth
        this.castMargin = parseInt(getComputedStyle(castCardEl).marginRight)
        this.castsPerScreen = Math.floor(castsEl.offsetWidth / (this.castWidth + this.castMargin))
        this.maxOffset = (this.casts.length - this.castsPerScreen) * (this.castWidth + this.castMargin)
          // Add touch event listeners
          let touchStartX = 0
  let touchEndX = 0
  let touchStartTime = 0
  let touchEndTime = 0
  castsEl.addEventListener('touchstart', event => {
    touchStartX = event.touches[0].clientX
    touchStartTime = Date.now()
  })
  castsEl.addEventListener('touchend', event => {
    touchEndX = event.changedTouches[0].clientX
    touchEndTime = Date.now()
    const touchDuration = touchEndTime - touchStartTime
    const touchDistance = touchEndX - touchStartX
    const swipeSpeed = Math.abs(touchDistance) / touchDuration // pixels per millisecond
    const pixelsToMove = Math.round(swipeSpeed * 100) // adjust the coefficient to control the sensitivity
    if (touchEndX > touchStartX) {
      this.scroll('right', pixelsToMove)
    } else if (touchEndX < touchStartX) {
      this.scroll('left', pixelsToMove)
    }
  })
      }, leftScrollImage() {
        if(this.currentOffset === 0){
            document.getElementById("left-arrow-grey").style.display="flex";
            document.getElementById("left-arrow-light").style.display="none";
        }else {
            document.getElementById("left-arrow-grey").style.display="none";
            document.getElementById("left-arrow-light").style.display="flex";
            
        }
    },
    rightScrollImage() {
        if(this.currentOffset === -this.maxOffset){
            document.getElementById("right-arrow-grey").style.display="flex";
            document.getElementById("right-arrow-light").style.display="none";
        }else{
            document.getElementById("right-arrow-grey").style.display="none";
            document.getElementById("right-arrow-light").style.display="flex";
        }
    },
    scroll(direction) {
        const delta = direction === 'left' ? this.castWidth + this.castMargin : -(this.castWidth + this.castMargin)
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

        this.$refs.casts.style.transform = `translateX(${this.currentOffset}px)`;

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
  .casts-carousel {
    position: relative;
    overflow: hidden;
  }
  
  .casts {
    display: flex;
    flex-wrap: nowrap;
    transition: transform 0.5s ease-in-out;
  }
  
  .cast-card {
  flex: 0 0 199px; /* Set a fixed width for each card */
  margin-right: 1rem; /* Add some space between cards */
  box-shadow: 0 0.25rem 0.5rem rgba(0, 0, 0, 0.1); /* Add a subtle box shadow */
  background-color: #1B1F32; /* Set a white background color */
}
.cast-name-font-style {
    font-family: 'Archivo';
    font-style: normal;
    font-weight: 600;
    font-size: 16px;
    line-height: 24px;
    color: #FFFFFF;
}
.cast-subinfo-font-style {
    font-family: 'Archivo';
    font-style: normal;
    font-weight: 400;
    font-size: 12px;
    line-height: 20px;
    color: #E0E3E8;
}
  
  .cast-card img {
    width: 100%;
  }
  
  .cast-info {
    background-color: #1B1F32;
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
  #left-arrow-light {
    display: none;
  }
  #right-arrow-grey {
    display: none;
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