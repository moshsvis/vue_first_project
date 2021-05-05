<template>
  <div>
    <div class="row">
      <div class="col-md-12">
         <div class="alert alert-warning" role="alert">
          <import></import>
        </div>
        <table class="table table-responsive table-sm table-hover">
          <thead>
            <tr>
              <th scope="col">Title</th>
              <th scope="col">Artist</th>
              <th scope="col">BPM</th>
              <th scope="col">Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(l, i) in myLocalFiles" :key="i">
              <td>{{ l.title }}</td>
              <td>{{ l.artist }}</td>
              <td>{{ l.bpm }}</td>
              <td>
                <!-- <i class="fas fa-trash" style="color:red"></i> -->
              </td>
            </tr>
          </tbody>
        </table>
       
      </div>
    </div>
  </div>
</template>

<script>
import Import from "../components/Import.vue";

export default {
  components: { Import },

  data: function () {
    return {
      cnt: 0,
      cntTotal: 0,
      localFileCount: 0,
      myLocalFiles: [],
      updating: false,
      currentDirectory: "",
    };
  },

  mounted() {
    let self = this
    this.myLocalFiles = JSON.parse(window.localStorage.getItem("CHLocalFiles"));
    this.localFileCount = this.myLocalFiles.length;
    const fs = window.require("fs");


   
  },
  methods: {
    updateCount: function (params) {
      this.updating = true;
      this.cnt = params;

      if (this.cnt === params) {
        this.updating = false;
      }
    },
    updateCountTotal: function (params) {
      this.cntTotal = params;
    },
  },
};
</script>

<style>
</style>