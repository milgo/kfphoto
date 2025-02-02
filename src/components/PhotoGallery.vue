<template>

<table border="0" width="70%">
  <tr>
    <td rowspan="2" width="60%" align="left">
      <h2>Krystyna Falkowska - Galeria </h2>
    </td>
    <td><button @click="this.downloadDb()">Pobierz albumy</button>
    </td>
  </tr>
  <tr>
    <td>
      Wgraj albumy: <input type="file" ref="doc" @change="uploadDb()" />
    </td>
  </tr>
</table>

  <table border="0">
    <tr align="left">
      <td v-for="g in galInfo" valign="top">
        <button @click="gallery = g; this.monthsDesc = JSON.parse(JSON.stringify(db.filter((gal) => gal.name === g.name)[0].data));">
          {{ g.name }}
        </button>
      </td>
    </tr>
  </table>
  <!--{{ gallery }}-->

  <table
    border="0"
    v-if="gallery.galleryJson && gallery.galleryJson.length > 0"
  >
    <tr>
      <td valign="top">
        <table border="1">
          <tr v-for="year in yearsOnly(gallery.galleryJson)" align="left">
            <td>
              <b>{{ year }}</b>
              <table border="0" cellpadding="0" cellspacing="0">
                <tr v-for="month in monthsOnly(gallery.galleryJson, year)">
                  <td valign="top">
                    <button @click="photoFilter = { year: year, month: month }">
                      {{ monthNames[month] }}
                    </button>
                  </td>
                  <td width="10px"></td>
                  <td width="200" valign="top">
                    <div
                      v-set="(desc = getMonthDescription(year, month))"
                    ></div>

                    <template v-if="desc">
                      <template v-if="desc.edit === true">
                        <VarInput
                          @blur="getMonthDescription(year, month).edit = false; storeMonthDesc(gallery.name)"
                          @enter="getMonthDescription(year, month).edit = false; storeMonthDesc(gallery.name)"
                          :value="desc.desc"
                          @valueChanged="
                            (value) =>
                              (getMonthDescription(year, month).desc = value)
                          "
                      /></template>
                      <template v-else
                        ><div
                          @click="getMonthDescription(year, month).edit = true"
                        >
                          - {{ desc.desc }}
                        </div></template
                      ></template
                    >
                  </td>
                </tr>
              </table>
            </td>
          </tr>
        </table>
      </td>
      <td valign="top">
        <table>
          <tr>
            <td>
              <table border="0">
                <tr>
                  <div
                    v-set="
                      (filteredGallery = gallery.galleryJson.filter((p) => {
                        return (
                          p.y === photoFilter.year && p.m === photoFilter.month
                        );
                      }))
                    "
                  ></div>
                  <template v-for="i in Math.ceil(filteredGallery.length / 5)">
                    <tr>
                      <template v-for="j in 5">
                        <div v-set="(index = (i - 1) * 5 + j - 1)"></div>
                        <td v-if="index < filteredGallery.length">
                          <img
                            width="150"
                            :src="
                              gallery.path.replace('\\\\', '\\') +
                              filteredGallery[index].p
                            "
                          />
                        </td>
                        <td v-else></td>
                      </template>
                    </tr>
                  </template>
                </tr>
              </table>
            </td>
          </tr>
        </table>
      </td>
    </tr>
  </table>


  <!--<textarea width="500" height="300">
<template >{{ this.optimaliseJson(gallery) }}</template> </textarea
  >-->
  <!--<div v-for="photo in data.sort((a, b) => b.year - a.year)">
    {{ photo.year }} {{ photo.filename }}
  </div>-->
</template>

<script setup>
const galleryDescJson = ref({});
</script>

<script>
import { ref } from "vue";
import configJson from "../assets/config.json";
import galeria1 from "../assets/data.json";
import galeria2 from "../assets/data.json";
import galeria3 from "../assets/data.json";
import galeria4 from "../assets/data.json";
import galeria5 from "../assets/data.json";
//import galleryJson from "../assets/data.json";
//import galleryDescJson from "../assets/months.json";
import VarInput from "./VarInput.vue";
export default {
  name: "PhotoGallery",
  props: {
  },
  components: {
    VarInput: VarInput,
  },
  mounted() {
    if(!window.localStorage.getItem("photogalleryKF")){
      var db = [];
      this.galInfo.forEach((g) => {
        var galObj = {};
        galObj.name = g.name;
        galObj.data = [];
        for (let i = 2000; i < 2025; i++) {
          for (let j = 1; j < 13; j++) {
              var obj = {};
              obj["year"] = i.toString();
              obj["month"] = j.toString();
              obj["desc"] = "aaa";
              obj["edit"] = false;
              galObj.data.push(obj);
            }
          }
          db.push(galObj);
        }
      );
      window.localStorage.setItem("photogalleryKF", JSON.stringify(db));
    }else{
      console.log(window.localStorage.getItem("photogalleryKF"));
    }
    this.db = JSON.parse(window.localStorage.getItem("photogalleryKF"));
  },
  data() {
    return {
      db: [],
      uploadFile: null,
      gallery: [], //galleryJson,
      monthsDesc: [],
      config: configJson,
      photoFilter: [],
      galInfo: [
        {
          path: "d:\\hhhh",
          name: "Galeria1",
          galleryJson: galeria1,
        },
        {
          path: "d:\\hhhh",
          name: "Galeria2",
          galleryJson: galeria2,
        },
        {
          path: "d:\\hhhh",
          name: "Galeria3",
          galleryJson: galeria3,
        },
        {
          path: "d:\\hhhh",
          name: "Galeria4",
          galleryJson: galeria4,
        },
        {
          path: "d:\\hhhh",
          name: "Galeria5",
          galleryJson: galeria5,
        },
      ],
      monthNames: [
        "",
        "styczeń",
        "luty",
        "marzec",
        "kwiecień",
        "maj",
        "czerwiec",
        "lipiec",
        "sierpień",
        "wrzesień",
        "październik",
        "listopad",
        "grudzień",
      ],
    };
  },
  methods: {
    optimaliseJson(json) {
      json.forEach((e) => {
        delete e["f"];
        delete e["d"];
      });
      return json;
    },
    getMonthDescription(year, month) {
      var ret = this.monthsDesc.filter((d) => {
        return d.year === year.toString() && d.month === month.toString();
      })[0];
      //console.log(this.monthsDesc);
      return ret;
    },
    storeMonthDesc(galname) {
      //console.log("storing " + galname + "...");
      this.db.filter((gal) => gal.name === galname)[0].data = this.monthsDesc;
      window.localStorage.setItem("photogalleryKF", JSON.stringify(this.db));
    },
    yearsOnly(data) {
      var yearsSet = new Set();
      data.forEach((element) => {
        yearsSet.add(element.y);
      });
      return Array.from(yearsSet).sort((a, b) => b - a);
    },
    monthsOnly(data, year) {
      var monthSet = new Set();
      data
        .filter((element) => {
          return element.y === year;
        })
        .forEach((element) => {
          monthSet.add(element.m);
        });
      return Array.from(monthSet).sort((a, b) => b - a);
    },
    getNow() {
      const today = new Date();
      const date = today.getFullYear()+""+(today.getMonth()+1)+""+today.getDate();
      const time = today.getHours()+""+ today.getMinutes() +""+ today.getSeconds();
      const dateTime = date +""+ time;
      return dateTime;
    },
    downloadDb() {
      const text = JSON.stringify(this.db); // Get the content from the textarea
      // Create a new Blob object with the text content
      const blob = new Blob([text], { type: 'text/plain' });
 
      // Create a temporary <a> element and set its download attribute to specify the file name
      const link = document.createElement('a');
      link.href = URL.createObjectURL(blob);
      link.download = this.getNow()+'.kfg'; // Replace with your desired file name
 
      // Programmatically click the link to trigger the download
      link.click();
      // Clean up the URL.createObjectURL by revoking the object URL after some time
      setTimeout(() => {
        URL.revokeObjectURL(link.href);
      }, 100);
    },
    uploadDb(){
      this.uploadFile = this.$refs.doc.files[0];
      const reader = new FileReader();
      if (this.uploadFile.name.includes(".kfg")) {
        reader.onload = (res) => {
          this.db = JSON.parse(res.target.result);
          window.localStorage.setItem("photogalleryKF", JSON.stringify(this.db));
          window.location.reload();
          //console.log(this.db);
        };
        reader.onerror = (err) => console.log(err);
        reader.readAsText(this.uploadFile);
      }else{
        alert("Niepoprawny format pliku!");
      }
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}

button {
  background: none !important;
  border: none;
  padding: 0 !important;
  /*optional*/
  font-family: arial, sans-serif;
  font-size: 16px;
  /*input has OS specific font-family*/
  color: #069;
  text-decoration: underline;
  cursor: pointer;
}
</style>
