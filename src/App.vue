<template>
  <div id="app" v-cloak>
    <div class="wrapper">

      <div class="gallery" v-if="images.length > 0">
        <div class="slider">
          <img :src="images[currentImageIndex]">
          <div class="arrows">
            <img src="./assets/images/Arrow-left.png"
              @click="prevImage"
              :class="{ arrowDisabled: currentImageIndex === this.borderPageIndexStart }">
            <img src="./assets/images/Arrow-right.png"
              @click="nextImage"
              :class="{ arrowDisabled: currentImageIndex === this.borderPageIndexEnd || this.images.length - this.currentImageIndex === 1 }">
          </div>
        </div>

        <div class="library">
          <div class="library-items">
            <div class="library-item"
              v-for="(image, index) in images"
              :key="index"
              :class="{ showingImages: index >= borderPageIndexStart && index <= borderPageIndexEnd }">
              <img class="library-item-image"
              :src="images[index]"
              @click="currentImageIndex = index"
              :class="{ selectedImage: currentImageIndex === index }">
              <div class="library-item-remove">
                <svg @click="removeImage(index)">
                  <use xlink:href="./assets/svg/sprite.svg#rubbish" />
                </svg>
              </div>
            </div>
          </div>
          <div class="library-nav">
            <span>{{ currentPage }} / {{ currentPages }}</span>
            <div class="arrows">
              <img src="./assets/images/Arrow-left.png"
                @click="prevPage"
                :class="{ arrowDisabled: this.borderPageIndexStart <= 0 }">
              <img src="./assets/images/Arrow-right.png"
                @click="nextPage"
                :class="{ arrowDisabled: this.images.length - this.borderPageIndexEnd <= 1 || this.images.length <= this.numderOfItemsPerPage }">
            </div>
          </div>
        </div>
      </div>

      <div class="gallery-empty" v-else>There are no images in the gallery.</div>

      <div class="buttons">
        <label for="upload-new-image">Upload new image</label>
        <input id="upload-new-image"
        type="file"
        accept="image/*"
        @change="uploadNewImage" />
        <button for="upload-from-flickr"
        @click="uploadFromFlickr">Upload from Flickr</button>
      </div>

    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data(){
    return{
      images:[
        require('./assets/images/Bubbles.jpg'),
        require('./assets/images/Forest.jpg'),
        require('./assets/images/Hubble_Extreme_Deep_Field.jpg'),
        require('./assets/images/huntington bancshares.jpg'),
        require('./assets/images/Lotus.jpg'),
        require('./assets/images/Mansion.jpg'),
        require('./assets/images/Moon.jpg'),
        require('./assets/images/Three.jpg'),
        require('./assets/images/Tie_dye.jpg'),
        require('./assets/images/Vaporwave_wallpaper.jpg'),

        // "Bubbles.jpg",
        // "Forest.jpg",
        // "Hubble_Extreme_Deep_Field.jpg",
        // "huntington bancshares.jpg",
        // "Lotus.jpg",
        // "Mansion.jpg",
        // "Moon.jpg",
        // "Three.jpg",
        // "Tie_dye.jpg",
        // "Vaporwave_wallpaper.jpg",
      ],
      currentImageIndex: 0,
      numderOfItemsPerPage: 9,
      borderPageIndexStart: 0,
      borderPageIndexEnd: 8,
    }
  },
  methods: {
    prevImage(){
      if(this.currentImageIndex > this.borderPageIndexStart){
        this.currentImageIndex--;
      }
    },
    nextImage(){
      if(this.currentImageIndex < this.borderPageIndexEnd && this.images.length - this.currentImageIndex > 1){
        this.currentImageIndex++;
      }
    },
    prevPage(){
      if(this.borderPageIndexStart > 0 && this.images.length > this.numderOfItemsPerPage){
        this.borderPageIndexStart -= this.numderOfItemsPerPage;
        this.borderPageIndexEnd -= this.numderOfItemsPerPage;
        this.currentImageIndex = this.borderPageIndexStart;
      }
    },
    nextPage(){
      if(this.images.length - this.borderPageIndexEnd > 1 && this.images.length > this.numderOfItemsPerPage ){
        this.borderPageIndexStart += this.numderOfItemsPerPage;
        this.borderPageIndexEnd += this.numderOfItemsPerPage;
        this.currentImageIndex = this.borderPageIndexStart;
      }
    },
    removeImage(index){
      if(index < this.currentImageIndex || index === this.images.length - 1 && index <= this.currentImageIndex){
        this.currentImageIndex -= 1;
      }
      if(index === this.images.length - 1 && index === this.borderPageIndexStart){
        this.prevPage();
      }
      this.images.splice(index, 1);
    },
    uploadNewImage(e) {
      const file = e.target.files[0];
      if(file){
        this.images.push(URL.createObjectURL(file));
        this.currentImageIndex = this.images.length - 1;
        this.borderPageIndexStart = (this.currentPages - 1) * this.numderOfItemsPerPage;
        this.borderPageIndexEnd = this.currentPages * this.numderOfItemsPerPage - 1;
      }
    },
    uploadFromFlickr(){
      let indexRandom = function getRandomInt(max) {
        return Math.floor(Math.random() * max);
      }
      fetch("https://api.flickr.com/services/rest/?method=flickr.photos.search&format=json&nojsoncallback=1&api_key=4457a3d846cd62e2f3850fa40b1e2b5f&extras=url_m&text=nature", {method: "GET"})
      .then(response => {
        console.log("Status:", response.status);
        return response.json();
      })
      .then(data => {
        let photoRandom = data.photos.photo[indexRandom(data.photos.photo.length - 1)];
        this.images.push(`https://farm${photoRandom.farm}.staticflickr.com/${photoRandom.server}/${photoRandom.id}_${photoRandom.secret}.jpg`);
      });
    }
  },
  computed:{
    currentPages() {
      return Math.ceil(this.images.length / this.numderOfItemsPerPage);
    },
    currentPage() {
      if (this.borderPageIndexStart === 0) {
        return 1;
      } else {
        return this.borderPageIndexStart / this.numderOfItemsPerPage + 1;
      }
    }
  },
}
</script>

<style>

/* general */
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body{
  background-color: #252525;
}
[v-cloak]{
  display: none;
}
.arrows{
  position: absolute;
  display: flex;
  justify-content: space-between;
}
.arrows > img{
  width: 1.2vw;
  transition: .2s;
}

/* dynamic */
.arrowDisabled{
  opacity: .5;;
}
div.showingImages{
  display: flex;
}
img.selectedImage{
  filter: brightness(1)
}

/* app */
#app {
  width: 70vw;
  margin: 2vw auto;
  padding: 4vw;
  background-color: #fff;
}
.wrapper{
  position: relative;
  width: 60vw;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: Arial, Helvetica, sans-serif;
  color: #252525;
}
.slider{
  position: relative;
  width: 50vw;
  height: 25vw;
  margin-bottom: .5vw;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.slider > img{
  max-width: 100%;
  max-height: 100%;
  width: auto;
}
.slider > .arrows{
  width: 54vw;
  top: 50%;
}
.library{
  width: 50vw;
  margin-bottom: .5vw;
  display: flex;
  flex-direction: column;
}
.library-items{
  margin-bottom: .5vw;
  display: grid;
  grid-template-columns: repeat(9, 1fr);
  grid-gap: .625vw;
}
.library-item{
  position: relative;
  width: 5vw;
  height: 5vw;
  display: none;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}
.library-item-image{
  width: auto;
  height: 100%;
  filter: brightness(.4);
}
.library-item-remove{
  position: absolute;
  bottom: -2vw;
  width: 100%;
  height: 30%;
  display: flex;
  justify-content: flex-end;
  align-items: center;
  background-color: #252525ab;
  transition: .2s;
}
.library-item-remove svg{
  width: 1vw;
  height: 1vw;
  fill: rgba(255, 255, 255, 0.5);
  transition: .2s;
}
.library-item-remove svg:hover{
  fill: #fff;
}
.library-item:hover .library-item-remove{
  bottom: 0;
}
.library-nav{
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
}
.library-nav > span{
  font-size: 2.2vw;
  font-weight: 700;
  }
.library-nav > .arrows{
  width: 13vw;
}
.gallery-empty{
  margin-bottom: 4vw;
}
.buttons{
  width: 30vw;
  display: flex;
  justify-content: space-between;
}
.buttons > label, .buttons > button{
  padding: .6vw 2vw;
  background-color: #252525;
  border: 0;
  border-radius: 2vw;
  color: #fff;
  font-size: 1.2vw;
  transition: .2s;
}
.buttons > label:hover, .buttons > button:hover{
  background-color: #464646;
}
.buttons > label:active, .buttons > button:active{
  background-color: #111111;
}
.buttons > input{
  display: none;
}
</style>

/* todo

* if response.status !== 200 make warning

* if !photoRandom.server make another request

*/