
<template>
  <div>
    <nav class="navbar navbar-expand-lg">
      <a class="navbar-brand" href="#"
        ><img
          src="https://d1muf25xaso8hp.cloudfront.net/https%3A%2F%2Fs3.amazonaws.com%2Fappforest_uf%2Ff1604791993078x861268982511117300%2FHOT%2520CRATES.png?w=96&h=78&auto=compress&fit=crop&dpr=2"
          style="height: 50px"
      /></a>
      <button
        class="navbar-toggler"
        type="button"
        data-toggle="collapse"
        data-target="#navbarColor03"
        aria-controls="navbarColor03"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>

      <div class="collapse navbar-collapse" id="navbarColor03">
        <ul class="navbar-nav mr-auto">
          <!-- <li class="nav-item active">
            <a class="nav-link" href="#"
              ><router-link to="/home">Home</router-link>
              <span class="sr-only">(current)</span>
            </a>
          </li> -->
          <li class="nav-item">
            <a class="nav-link" href="#"
              ><router-link to="/crates">Crates</router-link></a
            >
          </li>
         
          <!--<li class="nav-item">
            <a class="nav-link" href="#"><router-link to="/upload">Upload</router-link></a>
       
          
          </li> -->
          <li class="nav-item">
            <a class="nav-link" href="#"
              ><router-link to="/localfiles"
                >My Local Files </router-link
              >
            </a>
     
          </li>
          <!--
     <li class="nav-item">
        <a class="nav-link" href="#"><router-link to="/about">About</router-link></a>
      </li>
      <li class="nav-item dropdown">
        <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true" aria-expanded="false">Dropdown</a>
        <div class="dropdown-menu">
          <a class="dropdown-item" href="#">Action</a>
          <a class="dropdown-item" href="#">Another action</a>
          <a class="dropdown-item" href="#">Something else here</a>
          <div class="dropdown-divider"></div>
          <a class="dropdown-item" href="#">Separated link</a>
        </div>
      </li> -->
        </ul>
        <!-- <form class="form-inline my-2 my-lg-0">
          <input
            class="form-control mr-sm-2"
            type="text"
            placeholder="Search"
          />
          <button class="btn btn-secondary my-2 my-sm-0" type="submit">
            Search
          </button>
        </form> -->
      </div>
    </nav>

    <router-view />
  </div>
</template>

<script>


 const Store = window.require("electron-store");
 const store = new Store();

let localFiles = [];
let fileCountCompare = 0;
let fileCount = 0;

export default {
  
  data() {
    return {
      localFileCount: store.get("localFileCount")
    };
  },
  mounted() {
    var _ = require("lodash");

    if (!!window.localStorage.getItem("CHLocalFiles")) {
      this.localFileCount = _.size(
        JSON.parse(window.localStorage.getItem("CHLocalFiles"))
      );
    } else {
      this.localFileCount = 0;
    }
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
          parseFiles(localFiles);

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
                console.log(metadata);
                audioFile.id = fileCountCompare;
                audioFile.artist = metadata.common.artist;
                audioFile.title = metadata.common.title;
                audioFile.bpm = metadata.common.bpm;
                audioFile.genre = metadata.common.genre;

                localFilesWithMeta.push(audioFile);
              } catch (err) {
                console.log(err);
              } finally {
                fileCountCompare = fileCountCompare + 1;
                console.log(fileCount, fileCountCompare);
              }
            }

            console.log("END");

            window.localStorage.setItem(
              "CHLocalFiles",
              JSON.stringify(localFilesWithMeta)
            );
           
          }
          
        });
         this.localFileCount = fileCountCompare
    },
    clear: function () {
      window.localStorage.setItem("CHLocalFiles", []);
    },
  },
};
</script>

<style>
</style>
