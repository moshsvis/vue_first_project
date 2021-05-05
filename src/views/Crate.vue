<template>
  <div>
    <div v-if="this.processing">
      <div class="text-center">
        <div class="spinner-border" role="status">
          <span class="sr-only"
            >Loading...{{ this.localFilesCurrent }} of
            {{ this.localFilesLength }}</span
          >
        </div>
        Loading...<br>This may take a few minutes depending on how many local files you have
      </div>
    </div>
    <div class="row" v-if="!this.processing">
      <div class="col-md">
        <div class="m-4">
          <div class="float-left mr-2">
            <img height="60px" v-bind:src="crate.Image" />
          </div>
          <div class="mb-2">
            <h3>{{ crate.Title }}</h3>
            <button class="btn-primary float-right" v-on:click="getM3U('vdj')">
              Export to Virtual DJ
            </button>
            <button
              class="btn-primary float-right mr-2"
              v-on:click="getM3U('')"
            >
              Download M3U</button
            >&nbsp;
            <button
              class="btn-primary float-right mr-2"
              v-on:click="getPoolFiles()"
            >
              Download All Files
            </button>
            <!-- <button class="btn-primary float-right" v-on:click="getM3U('ser')">
              Export to Serato</button
            >&nbsp;
            <button class="btn-primary float-right" v-on:click="getM3U('rek')">
              Export to Rekordbox
            </button> -->
            <span class="lead" v-for="h in hackers" :key="h._id">
              {{ h.Full_Name }}
            </span>
          </div>
          <p>
            {{ crate.Description }}
          </p>
        </div>

        <table class="table table-responsive table-hover">
          <thead>
            <tr>
              <th scope="col">Track</th>
              <th scope="col">Local Files</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(t, i) in tracks" :key="t._id">
              <td>
                <span class="small"> {{ t.Artist }} - {{ t.Title }} </span>
              </td>
              <td>
                <div v-for="(f, idx) in t.localFiles" :key="i + '-' + idx">
                  <div>
                    <span style="white-space: nowrap">
                      <input
                        class="mr-2"
                        type="checkbox"
                        :checked="f.checked"
                        :id="i + '-' + idx"
                        :value="f.filePath"
                        @click="f.checked = !f.checked"
                      />

                      <label class="small" :for="i + '-' + idx">{{
                        f.filePath
                      }}</label>
                    </span>
                  </div>
                </div>
                <div>
                  <a
                    style="cursor: pointer"
                    class="badge badge-primary"
                    @click="getLocalFile(t._id, i)"
                  >
                    + Add Local File
                  </a>
                  <br />
                  <a
                    v-if="t.Pool_LNRP"
                    href="t.Pool_LNRP"
                    target="_blank"
                    class="badge badge-info"
                    >Late Night Record Pool</a
                  >
                  <a
                    v-if="t.Pool_DMS"
                    href="t.Pool_DMS"
                    target="_blank"
                    class="badge badge-info"
                    >Direct Music Service</a
                  >
                  <a
                    v-if="t.Pool_PromoOnly"
                    href="t.Pool_PromoOnly"
                    target="_blank"
                    class="badge badge-info"
                    >Promo Only</a
                  >
                  <a
                    v-if="t.Pool_DMS"
                    href="t.Pool_CrateGang"
                    target="_blank"
                    class="badge badge-info"
                    >Crate Gang</a
                  >
                  <a
                    v-if="t.Pool_HeavyHits"
                    href="t.Pool_HeavyHits"
                    target="_blank"
                    class="badge badge-info"
                    >Heavy Hits</a
                  >
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { constants } from "fs";

var dir = [];
var m3uContents = [];
function getFolder() {
  const dialog = window.require("electron").remote.dialog;
  dialog
    .showOpenDialog({
      properties: ["openDirectory"],
    })
    .then((result) => {
      return result.filePaths[0];
    });
}

export default {
  data: function () {
    return {
      loading: false,
      picked: [], //store for selected local tracks
      crateID: this.$route.params.crateID,
      crate: "",
      tracks: {}, //this is the main array that we are tracking. Tracks are from the crate. Each track can have multiple local files
      hackers: {},
      directory: "",
      processing: false,
      localFilesLength: "",
      localFilesCurrent: "",
    };
  },
  methods: {
    getLocalFile: function (trackId, ind) {
      const dialog = window.require("electron").remote.dialog;
      dialog
        .showOpenDialog({
          properties: ["openFile"],
        })
        .then((result) => {
          if (result) {
            this.tracks[ind].localFiles.push({ filePath: result.filePaths[0] });
            this.$set(this.tracks[ind]);
            console.log(this.tracks[ind].localFiles);
            // var t=  _.find(this.tracks, '_id', trackId);
            //  t.localFiles.push({'filePath':result.filePaths[0]})
            //    this.$set(this.tracks[ind] = t)
            //    console.log(this.tracks[ind] = t)
            // console.log(this.tracks.length)
            //return result.filePaths[0];
          }
        });
    },
    updateCount: function (length, count) {
      console.log("FROM FUNC");
      this.localFilesLength = length
    },
    getM3U: function (software) {
      const fs = window.require("fs");
      switch (software) {
        case "vdj":
          var writer = require("m3u").extendedWriter();
          let fff = this.tracks.filter((obj) =>
            obj.localFiles.some((locFile) => locFile.checked === true)
          );
          //fff contains a list of tracks where one or more local files are checked. We need to extract only the localfile that are checked
          fff.forEach(function (item, i) {
            item.localFiles.forEach(function (l, id) {
              if (l.checked === true) {
                writer.file(l.filePath);
              }
            });
          });
          try {
            fs.writeFileSync(
              "/users/glennrenda/documents/virtualdj/playlists/" +
                this.crate.Title.trim().replace("/", "_") +
                ".m3u",
              writer.toString(),
              "utf-8"
            );
            alert(
              "Your file has been downloaded to VDJ. Please refresh your playlists in VDJ"
            );
          } catch (e) {
            console.log("Error:", e);
            alert("Failed to save the file !");
          }
          break;
        //todo:add additional software here
        default: {
          const dialog = window.require("electron").remote.dialog;
          dialog
            .showOpenDialog({
              properties: ["openDirectory"],
            })
            .then((result) => {
              var writer = require("m3u").extendedWriter();
              //lets get the list of tracks where localfiles is checked
              let fff = this.tracks.filter((obj) =>
                obj.localFiles.some((locFile) => locFile.checked === true)
              );
              //fff contains a list of tracks where one or more local files are checked. We need to extract only the localfile that are checked
              fff.forEach(function (item, i) {
                item.localFiles.forEach(function (l, id) {
                  if (l.checked === true) {
                    writer.file(l.filePath);
                  }
                });
              });

              try {
                fs.writeFileSync(
                  result.filePaths[0] +
                    "/" +
                    this.crate.Title.trim().replace("/", "_") +
                    ".m3u",
                  writer.toString(),
                  "utf-8"
                );
              } catch (e) {
                console.log("Error:", e);
                alert("Failed to save the file !");
              }
            });
        }
      }
    },
  },
  mounted() {
    let tempTracks = {};
    this.processing = true;
    let self = this;
    axios
      .post(
        "https://app.cratehackers.com/api/1.1/wf/getcrate?crateid=" +
          this.crateID +
          "&api_token=996781a5b67881debb0cfe20770c76b3"
      )
      .then((response) => {
        (this.crate = response.data.response.crate),
          (tempTracks = response.data.response.tracks),
          (this.hackers = response.data.response.hackers);

        let count = 1;

        //add localFiles to tracks
        //for (const item of tempTracks) {
        tempTracks.forEach((item, i) => {
          console.log("start item " + count + " of " + tempTracks.length);
          this.updateCount(tempTracks.length, count);

          var filesToSearch;

          var filesToSearch = JSON.parse(
            window.localStorage.getItem("CHLocalFiles")
          );

          //search local tracks

          const MiniSearch = window.require("minisearch");

          let miniSearch = new MiniSearch({
            fields: ["title", "artist"], // fields to index for full-text search
            storeFields: ["filePath", "title", "artist"], // fields to return with search results
          });

          // Index all documents
          miniSearch.addAll(filesToSearch, {
            fields: ["title", "artist"],
            prefix: true,
          });

          // Search with default options
          let res = miniSearch.search(item.Title + " " + item.Artist);

          //if results = index of 0 then mark as selected. This will check the checkbox
          item.localFiles = res.slice(0, 5); //limit to 5 results

          res.forEach(function (r, i) {
            r.checked = i === 0 ? true : false;
          });
          count = count + 1;
        });
        console.log("END");
        this.tracks = tempTracks;
        this.processing = false;
      });
    this.directory = window.localStorage.getItem("CHLocalFilesDirectory");
  },
};
</script>
      