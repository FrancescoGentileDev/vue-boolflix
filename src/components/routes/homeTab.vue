<template>
  <div>
    <video-section logo="Rick_and_Morty.svg" title="Le avventure di nonno rick e suo nipote morty attraverso universi paralleli e CETRIOLI" video="rick_and_morty.mp4"/>

    <content-section
      :titles="popularTV.results"
      :small="false"
      :showCarousel="true"
      :title="popularTV.title"
    />
    <content-section
      :titles="popularFilm.results"
      :showCarousel="true"
      :small="true"
      :title="popularFilm.title"
    />

    <content-section
      v-for="(item, index) in byCategoryMovie"
      :key="index"
      :titles="item.results"
      :showCarousel="true"
      :small="true"
      :title="item.title"
    />
  </div>
</template>

<script>
import ContentSection from "../contentSection.vue";
import Call from "@/call";
// eslint-disable-next-line no-unused-vars
import axios from "axios";
import VideoSection from "../videoSection.vue";
export default {
  components: {
    ContentSection,
    VideoSection,
  },
    data() {

    return {


      pickProfile: false,
      pickedProfile: "",
      baseUrl: "https://api.themoviedb.org/3",
      searchText: "",
      loading: true,

      popularFilm: [],
      popularTV: [],
      byCategoryMovie: [],
      byCategoryTV: [],

      callFilm: [],
      callTV: [],

      allMovieCategory: [],
      allTvCategory: []
    };

  },
  async created() {
    let popularMovie = new Call({ language: "it-IT", adult: false });

    await popularMovie.getArrayTitle("Popular Now", "movie", "popular").then((res) => {
      this.popularFilm = res;
    });
    
    let popularTV = new Call({ language: "it-IT", adult: false });

    await popularTV.getArrayTitle("Last Release", "tv", "popular").then((res) => {
      this.popularTV = res;
    });

    let categoryMovie = new Call({ language: "it-IT", adult: false });

    let allCategoryMovie = await categoryMovie.makeCall("genre", "movie", "list");

    this.allMovieCategory = allCategoryMovie;

    let randomCategoryMovie = allCategoryMovie.genres.filter(() => {
      let rand = Math.round(Math.random());
      return rand;
    });

      let category = new Call({ language: "it-IT", adult: false});
      category.type = "movie"
    randomCategoryMovie.forEach((genre) => {
       let rand= Math.floor(Math.random()* 15 + 1)

      category.data.params.page= rand
      category.getByCategory(genre.id).then((value) => {
        this.byCategoryMovie.push({ results: value.results, title: genre.name });
      });
    });

    this.$emit("loaded", true)
  },
};
</script>

<style lang="scss" scoped></style>
