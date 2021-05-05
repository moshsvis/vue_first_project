<template>
  <div class="row">
    <div class="col-md-6">
          <button class="btn btn-link" v-on:click="searchRecursive()"> <i class="fas fa-file-import"></i> Import Local Files</button>
{{ this.localFileCountCurrent }} of {{ this.localFileCount }}
    </div>
    <div class="col-md-6 mt-3">
           Current Directory: {{this.currentDirectory}}
    </div>

  </div>
</template>


<script>
let localFiles = [];
let fileCountCompare = 0;
let fileCount = 0;
let self = this;

export default {
  data: function () {
    return {
      myLocalFiles: [],
      localFileCount: 0,
      localFileCountCurrent: 0,
      importing: false,
      currentDirectory: window.localStorage.getItem("CHLocalFilesDirectory")
    };
  },

  methods: {
  
    searchRecursive() {
      const dialog = window.require("electron").remote.dialog;

      dialog
        .showOpenDialog({
          properties: ["openFile", "openDirectory"],
        })
        .then((result) => {
          var dir = result.filePaths[0];
          window.localStorage.setItem("CHLocalFilesDirectory", dir);
          window.localStorage.setItem("CHLocalFiles", []);
          localFiles = [];
          const fs = window.require("fs");
          const path = window.require("path");

          function walkDir(dir, callback) {
            fs.readdirSync(dir).forEach(function (f) {
              let dirPath = path.join(dir, f);
              let isDirectory = fs.statSync(dirPath).isDirectory();
              isDirectory
                ? walkDir(dirPath, callback)
                : callback(path.join(dir, f));
              fs.close;
            });
          }

          //get all local files
          walkDir(dir, function (filePath) {
            localFiles.push({ filePath });
          });
          //lets get the number of local files. We will compare later so we know when async is done
          fileCount = localFiles.length;
          this.fileCount = fileCount;
          this.localFileCount = fileCount;
          parseFiles(localFiles);
          self = this;
          let localFilesWithMeta = [];
          // it is required to declare the function 'async' to allow the use of await
          async function parseFiles(audioFiles) {
            fileCountCompare = 0;

            const mm = window.require("music-metadata");

            for (const audioFile of audioFiles) {
              try {
                // await will ensure the metadata parsing is completed before we move on to the next file
                const metadata = await mm.parseFile(audioFile.filePath);
                // Do great things with the metadata
                //console.log(metadata);
                audioFile.id = fileCountCompare;
                audioFile.artist = metadata.common.artist;
                audioFile.title = metadata.common.title;
                audioFile.bpm = metadata.common.bpm;
                audioFile.genre = metadata.common.genre;

                localFilesWithMeta.push(audioFile);
              } catch (err) {
                console.log(err, audioFile);
              } finally {
                fileCountCompare = fileCountCompare + 1;
                console.log(fileCount, fileCountCompare);
                self.updateC(fileCountCompare);
              }
            }

            console.log("END");

            window.localStorage.setItem(
              "CHLocalFiles",
              JSON.stringify(localFilesWithMeta)
            );
          }
        });
    },
    updateC(count) {
      this.localFileCountCurrent = count;
    },
  },
};
</script>

