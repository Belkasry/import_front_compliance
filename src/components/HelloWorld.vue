<template>
  <v-container>
    <div>
      {{ breadcrumbs }}
      <v-breadcrumbs
          :items="breadcrumbs_formated"
          large
      >
      </v-breadcrumbs>
    </div>
    <v-text-field
      variant="outlined"
      class="w-66 my-2"
      v-model="search"
      append-inner-icon="mdi-magnify"
      label="Search"
      single-line
      hide-details
    ></v-text-field>
    <v-data-table
      :headers="headers"
      :items="articles"
      :search="search"
    >
    </v-data-table>
    <v-dialog v-model="dialog_annexe" max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">Annexe</span>
        </v-card-title>
        <v-card-text>
          {{ annexe.intitule_annexe }}
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" @click="dialog_annexe=false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
const headers = [
  {text: 'Parents', value: 'parents', width: '20%'},
  {text: 'Titre', value: 'Titre'},
  {text: 'Article', value: 'article'},
  {text: 'Type', value: 'article_type'},
  {text: 'Remarque', value: 'remarque_article'},
  {text: 'Annexe', value: 'annexe'},
  {text: 'Blob', value: 'blob'},
]
// write articles in data() and use it

export default {
  props: {
    articles: Array,
    breadcrumbs: Array,
  },
  data() {
    return {
      dialog_annexe: false,
      annexe: {intitule_annexe: ''},
      search: '',
      headers: headers,
      breadcrumbs_formated:[]
    }
  },
   computed() {
    this.breadcrumbs_formated=this.breadcrumbs.map((item)=>{return {
      text: item,
      disabled: false,
    }})
  },
  methods: {
    showAnnexe(item) {
      this.annexe = item.raw.annexe
      console.log(JSON.stringify(this.annexe))
      this.dialog_annexe = true
    }
  }
}
</script>
