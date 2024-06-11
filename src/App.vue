<template>
  <div id="app">
    <a v-if="link" :href="link" v-text="link" rel="noopener" target="_blank">
    </a>
    <p v-else>Loading...</p>
  </div>
</template>

<script>
const { connect, Field, FieldIntentCtx } = require("datocms-plugins-sdk");
export default {
  name: "app",
  components: {},
  data() {
    return {
      plugin: false,
      link: "",
    };
  },
  mounted() {
    this.initPlugin();
    // if (process.env.NODE_ENV === "production") {
    //   await this.initPlugin();
    // } else {
    //   await this.loadSiteMap("/dato-route-map.json");
    //   this.checkLink("RdiRecipeRecord");
    //   return true;
    // }
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
        .catch(function (error) {
          console.log(error);
        });
    },
    async checkLink(model) {
      const entry = this.data.find((el) => {
        if (el.locale) {
          return el.model === model && el.locale === this.plugin.locale;
        } else {
          return el.model === model;
        }
      });

      // console.log(this.plugin);
      if (entry) {
        if (entry.params.length) {
          await entry.params.forEach(async (el) => {
            const snakeCase = this.camelToSnake(el);
            let path = "";
            const locale = entry.locale
              ? this.plugin.site.attributes.locales.find((locale) =>
                  locale.includes(entry.locale)
                )
              : this.plugin.site.attributes.locales[0];
            if (snakeCase.indexOf(".") !== -1) {
              const second = el.split(".")[1];
              const firstSlug = snakeCase.split(".")[0];

              const { SiteClient } = require("datocms-client");
              const client = new SiteClient(this.plugin.currentUserAccessToken);
              const item = await client.item.find(
                this.plugin.item.attributes[firstSlug]
              );

              path = item[second][locale] || item[second];
            } else {
              console.log(
                this.plugin.item.attributes[snakeCase][locale],
                this.plugin.item.attributes[snakeCase]
              );
              path =
                this.plugin.item.attributes[snakeCase][locale] ||
                this.plugin.item.attributes[snakeCase];
            }
            entry.path = entry.path
              .replace(`:${el}?`, path)
              .replace(`:${el}`, path);
            this.link =
              this.plugin.plugin.attributes.parameters.previewUrl + entry.path;
          });
        } else {
          this.link =
            this.plugin.plugin.attributes.parameters.previewUrl + entry.path;
        }
      }
    },
    initPlugin() {
      const _this = this;
      connect({
        manualFieldExtensions() {
          return [
            {
              id: "preview",
              name: "My preview widget",
              type: "editor",
              fieldTypes: ["json"],
            },
          ];
        },
        async renderFieldExtension(id, ctx) {
          const record = _this.snakeToPascal(ctx.itemType.attributes.api_key);
          _this.plugin = ctx;
          await _this.loadSiteMap();
          await _this.checkLink(record);
        },
      });
      return;
    },
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
</style>
