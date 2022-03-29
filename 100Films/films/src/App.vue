<template>
  <div>
    <main>
      <div class="container">
        <div class="button-wrapper">
          <button class="button-wrapper__button" @click="getFilms">
            Hämta 100 filmer
          </button>
          <div v-if="loading" class="button-wrapper__loader"></div>
        </div>
        <div class="wrapper" v-for="film in films" :key="film">
          <div class="wrapper__card" v-for="item in film.results" :key="item">
            <img :src="imageUrl + item.poster_path" alt="cover-image" />
            <h1 class="wrapper__card__title">{{ item.title }}</h1>
            <p class="wrapper__card__text">{{ item.overview }}</p>
            <p class="wrapper__card__grade">Betyg: {{ item.vote_average }}</p>
            <p class="wrapper__card__votes">
              Antal röster: {{ item.vote_count }}
            </p>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>
<script>
export default {
  data() {
    return {
      loading: false,
      films: [],
      imageUrl: "https://image.tmdb.org/t/p/w200",
      apiKey: "api_key=XXX&",
      baseUrl: "https://api.themoviedb.org/3",
    };
  },

  methods: {
    async getFilms() {
      this.loading = true;
      const numPages = 5;
      let apiRequests = [];
      for (let i = 1; i <= numPages; i++) {
        apiRequests.push(
          fetch(
            this.baseUrl +
              "/discover/movie?sort_by=popularity.desc&" +
              this.apiKey +
              "page=" +
              i
          )
        );
      }
      try {
        const res = await Promise.all(apiRequests);
        const data = await Promise.all(res.map((r) => r.json()));
        this.films = data;
        this.loading = false;
      } catch {
        throw Error("Promise failed");
      }
    },
  },
};
</script>

<style>
@import "./assets/base.css";
.container {
  margin: 0 auto;
  width: 80%;
  margin-top: 80px;
  margin-bottom: 40px;
}
.button-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 70px;
}
.button-wrapper__button {
  font-size: 50px;
  color: #f3f3f3;
  background-color: #3498db;
  padding: 20px;
  border: none;
  margin-bottom: 30px;
  border-radius: 10px;
  cursor: pointer;
}
.button-wrapper__loader {
  border: 16px solid #f3f3f3;
  border-top: 16px solid #3498db;
  border-radius: 50%;
  width: 36px;
  height: 36px;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
.wrapper {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  grid-auto-rows: minmax(100px, auto);
  gap: 30px;
}
.wrapper__card {
  margin-bottom: 20px;
}
img {
  width: 100%;
}
.wrapper__card__title {
  font-size: 19px;
  margin-top: 8px;
  margin-bottom: 6px;
  line-height: normal;
  font-weight: bold;
}
.wrapper__card__text {
  line-height: 150%;
}
.wrapper__card__grade {
  margin-top: 10px;
  font-size: 13px;
  font-weight: bold;
}
.wrapper__card__votes {
  font-weight: bold;
  font-size: 13px;
}
</style>
