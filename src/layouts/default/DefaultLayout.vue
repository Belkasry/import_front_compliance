<template>
  <v-app>
    <div style="background: black;" class="py-4">
      <v-btn @click.stop="drawer = !drawer" class="mx-5" color="white" outlined><v-icon  >mdi-file-tree</v-icon></v-btn>
      <v-btn @click="dialog_import=true" tonal color="info"> <v-icon>mdi-file-import</v-icon>Importer</v-btn>
    </div>
    <v-navigation-drawer
        width="60%"
        absolute
        right
        v-model="drawer"
        >
      <arbre-texte @fetch-parent="callback"></arbre-texte>
    </v-navigation-drawer>
    <v-main>
      <v-card>
        <v-card-text>
          <v-dialog v-model="dialog_import" max-width="500px">
            <v-card>
              <v-card-title>
                <span class="headline">Importer un fichier</span>
                <v-alert v-if="error" type="error">
                  {{ error }}
                </v-alert>
              </v-card-title>
              <v-card-text>
                <v-file-input
                    v-model="excelFile"
                    accept=".xls,.xlsx"
                    label="Import Excel File"
                    variant="outlined"
                    prepend-inner-icon="mdi-file-excel"
                    density="compact"
                    color="primary"
                ></v-file-input>
                <template v-if="excelFile">
                  <v-select
                      v-model="columns.column_dernier_titre"
                      :items="excelColumns"
                      label="Column Dernier Titres"
                      outlined
                      dense
                      item-text="column"
                      item-value="order"
                      clearable="true"
                      color="primary"></v-select>
                  <v-select
                      v-model="columns.column_article"
                      :items="excelColumns"
                      label="Column Article"
                      outlined
                      dense
                      clearable="true"
                      item-text="column"
                      item-value="order"
                      color="primary"></v-select>
                  <v-select
                      v-model="columns.column_type"
                      :items="excelColumns"
                      label="Column Type"
                      outlined
                      dense
                      clearable="true"
                      item-text="column"
                      item-value="order"
                      color="primary"></v-select>
                  <v-select
                      v-model="columns.column_remarque"
                      :items="excelColumns"
                      label="Column Remarque"
                      outlined
                      dense
                      clearable="true"
                      item-text="column"
                      item-value="order"
                      color="primary"></v-select>
                  <v-select
                      v-model="columns.column_annexe"
                      :items="excelColumns"
                      label="Column Annexe"
                      outlined
                      dense
                      clearable="true"
                      item-text="column"
                      item-value="order"
                      color="primary"></v-select>
                </template>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" @click="dialog_import=false">Cancel</v-btn>
                <v-btn color="blue darken-1" @click="submitMap">Importer</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <HelloWorld :articles="articles" :breadcrumbs="breadcrumbs"/>
        </v-card-text>
      </v-card>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from "@/components/HelloWorld.vue";
import axios from "axios";
import ArbreTexte from "@/components/ArbreTexte.vue";

const treeData = {
  name: 'TITRE TEXTE HSE',
  children: []
}

export default {
  components: {
    ArbreTexte,
    HelloWorld,
  },
  data() {
    return {
      dialog_import: false,
      drawer: false,
      error: null,
      treeData,
      treeDatas: [],
      breadcrumbs: [],
      articles: [],
      columns: {
        column_texte: 1,
        column_dernier_titre: 5,
        column_article: 6,
        column_type: 7,
        column_annexe: 8,
        column_remarque: 9,
      },
      excelFile: null,
      excelColumns: [
        {order: 1, column: "A"},
      ]
    }
  },
  mounted() {
    this.fetch();
  },
  watch: {
    excelFile: function () {
      this.getHeader();
    },
  },
  methods: {
    async getHeader() {
      const formData = new FormData();
      if (this.excelFile) {
        let filesArray = Array.isArray(this.excelFile)
          ? this.excelFile
          : [this.excelFile];
        Array.from(filesArray).forEach((file) => {
          formData.append("file", file);
        });
      }
      await axios
        .post("/api/excel/header", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then((response) => {
          this.excelColumns = response.data;
        })
        .catch((error) => {
          this.error=error.response.data.message;
          console.log(error);
        });
    },

    async submitMap() {
      const formData = new FormData();
      formData.append('column_texte', this.columns.column_texte);
      formData.append('column_dernier_titre', this.columns.column_dernier_titre);
      formData.append('column_article', this.columns.column_article);
      formData.append('column_type', this.columns.column_type);
      formData.append('column_annexe', this.columns.column_annexe);
      formData.append('column_remarque', this.columns.column_remarque);

      if (this.excelFile) {
        let filesArray = Array.isArray(this.excelFile)
          ? this.excelFile
          : [this.excelFile];
        Array.from(filesArray).forEach((file) => {
          formData.append("file", file);
        });
      }
      await axios
        .post("/api/import", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then((response) => {
          console.log(response.data);
          this.dialog_import = false;
          this.fetch()
        })
        .catch((error) => {
          this.error=error.response.data.message;
        });
    },
    fetch() {
      this.init()
      this.getTreeData()
    },
    async init() {
      const response = await axios.get('/api/text')
      this.articles = await response.data
    },
    async callback(model) {
      let id = model.id;
      let response;
      if (id) {
        response = await axios.get('/api/text/' + id)
        this.articles = response.data
        if (this.articles.length > 0) {
          const response_breadcrumbs = (await axios.get('/api/parents/' + id));
          this.breadcrumbs = response_breadcrumbs.data;
        }
      } else {
        let id = model.texte_id;
        response = await axios.get('/api/text_hse/' + id);
        this.articles = response.data
        if (this.articles.length > 0) {
          this.breadcrumbs = [this.articles[0].texte.intitule_text]
        }
      }
    },
    async getTreeData() {
      const response = await axios.get('/api/tree')
      this.treeDatas = await response.data;

    }
  }
}
</script>
