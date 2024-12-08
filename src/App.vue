<template>
  <div class="weather" :class="weatherClass">
    <div class="container">
      <div class="card weather-form">
        <input type="text" @keyup.enter="weatherSearch" v-model="searchQuery" class="weather-form__input" placeholder="Enter city"/>
        <button @click="weatherSearch" class="weather-from__btn">Search</button>
      </div>
      <div class="card weather-load" v-if="loading">Loading...</div>
      <div class="card weather-error" v-if="error">ERROR</div>
      <div class="weather-info" v-show="!error && !loading && location && temperature && description">
        <div class="weather-info__text">
          <p class="card">{{location}}</p>
          <p class="card">{{temperature}}C</p>
          <p class="card">{{description}}</p>
        </div>
      </div>
    </div>
  <div class="weather-bg">
    <div>
      <img class="weather-bg__img bg" src="./assets/bg.jpg" alt="App Background">
      <img class="weather-bg__img sunny" src="./assets/sunny.jpg" alt="Sunny">
      <img class="weather-bg__img rainy" src="./assets/rainy.jpg" alt="Rainy">
    </div>
  </div>
  </div>
</template>

<script>
export default {
  data(){
    return {
      location: '',
      temperature: 0,
      description: '',
      loading: false,
      error: false,
      searchQuery: '',
    };
  },
  computed: {
    weatherClass() {
      if(this.description.includes("Sunny") && !this.error){
        return 'sunny'
      } else if(!this.description.includes("Sunny") && this.description.length > 1 && !this.error) {
        return 'rainy'
      } else {
        return ''
      }
    }
  },
  methods: {
    weatherSearch() {
      this.loading = true;
      this.error = false;
      fetch(`http://api.weatherapi.com/v1/current.json?key=${process.env.KEY}&q=${this.searchQuery}`)
          .then(response=>response.json())
          .then(data=>{
            this.loading = false;
            this.error = false;
            this.location = data.location.name;
            this.temperature = data.current.temp_c;
            this.description = data.current.condition.text
            this.resetSearchQuery()
          })
          .catch(error=>{
            this.error = true;
            this.loading = false;
            console.error(error);
            this.resetSearchQuery()
          })
    },
    resetSearchQuery() {
      this.searchQuery = '';
    }
  }
}
</script>