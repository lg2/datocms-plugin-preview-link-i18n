<template>
  <div id="app">
    <a v-if="link" :href="link" v-text="link" rel="noopener" target="_blank">
    </a>
    <p v-else>Loading...</p>
  </div>
</template>

<script>
const DatoCmsPlugin = require("datocms-plugins-sdk");
export default {
  name: "app",
  components: {},
  data() {
    return {
      plugin: false,
      link: "",
    };
  },
  async mounted() {
    if (process.env.NODE_ENV === "production") {
      await this.initPlugin();
    } else {
      await this.loadSiteMap("/dato-route-map.json");
      this.checkLink("RdiRecipeRecord");
      return true;
    }
  },
  watch: {},
  methods: {
    async loadSiteMap(url = "") {
      await fetch(
        url ? url : this.plugin.plugin.attributes.parameters.dataMapUrl
      )
        .then(async (res) => {
          const data = await res.json();
          this.data = data;
        })
        .catch(function(error) {
          console.log(error);
        });
    },
    checkLink(model) {
      const entry = this.data.find((el) => {
        if (el.locale) {
          return el.model === model && el.locale === this.plugin.locale;
        } else {
          return el.model === model;
        }
      });
      if (entry) {
        console.log(entry);
        if (entry.params.length) {
          console.log(this.plugin);
          entry.params.forEach((el) => {
            const snakeCase = this.camelToSnake(el);
            let path = "";
            if (snakeCase.indexOf(".") !== -1) {
              const [first, second] = snakeCase.split(".");
              console.log(
                snakeCase,
                this.plugin.item.attributes,
                this.plugin.item.attributes[first][second]
              );
              path = entry.locale
                ? this.plugin.item.attributes[first][second][entry.locale]
                : this.plugin.item.attributes[first][second];
            } else {
              console.log(
                snakeCase,
                this.plugin.item.attributes,
                this.plugin.item.attributes[snakeCase]
              );
              console.log("entry.path", entry.path);

              path = entry.locale
                ? this.plugin.item.attributes[snakeCase][entry.locale]
                : this.plugin.item.attributes[snakeCase];
            }

            entry.path = entry.path.replace(`:${el}`, path);
          });
        }
        this.link =
          this.plugin.plugin.attributes.parameters.previewUrl + entry.path;
      }
    },
    async initPlugin() {
      this.plugin = await DatoCmsPlugin.init();
      console.log(this.plugin);
      this.plugin.startAutoResizer();
      // const fieldValue = this.plugin.getFieldValue(this.plugin.fieldPath);
      const record = this.snakeToPascal(
        this.plugin.itemType.attributes.api_key
      );
      await this.loadSiteMap();
      this.checkLink(record);

      // if (fieldValue) {
      //   this.dataJson = JSON.parse(fieldValue);
      // }
    },
    // updateDatoField() {
    //   this.plugin.setFieldValue(
    //     this.plugin.fieldPath,
    //     JSON.stringify(this.dataJson)
    //   );
    // },
    snakeToPascal(snakeCase) {
      const words = snakeCase.split("_");
      const pascalCaseWords = words.map(
        (word) => word.charAt(0).toUpperCase() + word.slice(1).toLowerCase()
      );
      return pascalCaseWords.join("") + "Record";
    },
    camelToSnake(camelCase) {
      return camelCase.replace(
        /[A-Z]/g,
        (letter) => `_${letter.toLowerCase()}`
      );
    },
  },
};
</script>

<style lang="scss">
@import "https://unpkg.com/datocms-plugins-sdk/dist/sdk.css";

.table-container {
  width: 100%;
  margin-top: 30px;
  overflow: auto;
  td {
    min-width: 200px;
  }
}
</style>
