<template>
  <div>
    <!-- <button class="btn btn-warning" @click="db()">Prime</button> -->
    <h2>Welcome to the Crate Hackers App v0.1.8</h2>
  </div>
</template>

<script>
// const sqlite3 = window.require("sqlite3")

export default {
  data() {
    return {};
  },
  mounted() {},
  methods: {
    //     db(){
    //       let db = new sqlite3.Database('./database/m.db');
    // let sql = "INSERT INTO Playlist (title) VALUES ('cratehackers')";
    // db.run(sql, function(err) {
    //   if (err) {
    //     return console.error(err.message);
    //   }
    //   console.log('Rows inserted');
    // });
    //     }
  },
};
</script>
<style scoped>
h2{
  text-align: center;
}
</style>
