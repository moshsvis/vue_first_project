<template>
  <div>
    <ul class="list-group">
      <li
        v-for="c in crates"
        :key="c._id"
        v-on:click="go(c._id)"
        class="list-group-item d-flex justify-content-between align-items-center"
      >
        {{ c.Title }}

        
        <span v-if="c.Tracks" class="badge badge-primary badge-pill">
          {{ trackCount(c.Tracks) }} Tracks</span
        >
      </li>
    </ul>
    <button class="btn btn-primary" v-on:click="loadMore">Load More</button>
  </div>
</template>

<script>
import router from "../router";
import axios from "axios";
export default {
  data() {
    return {
      crates: [],

      busy: false,
      remaining: 0,
      cursor: 50,
    };
  },
  mounted() {
    axios
      .get(
        "https://app.cratehackers.com/api/1.1/obj/crate?sort_field=title&api_token=996781a5b67881debb0cfe20770c76b3&limit=50"


      )
      .then(
        (response) => (
          (this.crates = response.data.response.results),
          (this.remaining = response.data.response.remaining)
        )
      );
  },
  methods: {
    go: (id) => {
      router.push({ name: "Crate", params: { crateID: id } });
    },
    trackCount: function (tracks) {
      if (!!tracks) {
        return tracks.length;
      }
    },
       
    
    loadMore: function () {
      if (this.remaining > 0) {
        this.busy = true;

        setTimeout(() => {
          axios
            .get(
              "https://app.cratehackers.com/api/1.1/obj/crate?sort_field=title&api_token=996781a5b67881debb0cfe20770c76b3&limit=50&cursor=" +
                this.cursor
            )
            .then(
              (response) => (
                (this.crates = response.data.response.results),
                (this.remaining = response.data.response.remaining),
                (this.cursor = this.cursor + 50),
                // (this.cratecount = response.data.response.cratecount),
                console.log(response.data.response)
              )
            );

          this.busy = false;
        }, 1000);
      }
    },
  },
};
</script> 

<style>
</style>