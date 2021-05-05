<template>
  <div>
    <button @click="getVDJXML" class="btn-primary">Get VDJ XML</button>
  </div>
</template>

<script>
const fs = window.require("fs");

export default {
  methods: {
    getVDJXML: function () {
      var filepath = "/users/glennrenda/documents/virtualdj/database.xml";
      fs.readFile(filepath, "utf-8", (err, data) => {
        if (err) {
          alert("An error ocurred reading the file :" + err.message);
          return;
        }
        var xml2js = require("xml2js")
        var parseString = require("xml2js").parseString;
        var xml = data;
        var xmlResult;
        parseString(xml, {normalize: true},function (err, result) {
          xmlResult = result;
        });
    
    
     // convert JSON object to XML
        const builder = new xml2js.Builder();
        const saveXML = builder.buildObject(xmlResult);

        // write updated XML string to a file
        fs.writeFile('/users/glennrenda/documents/virtualdj/database_NEW.xml', decodeURIComponent(saveXML), (err) => {
            if (err) {
                throw err;
            }

            console.log(`Updated XML is written to a new file.`);
        });


      });
    },
  },
};
</script>

<style>
</style>