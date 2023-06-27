<template>
  <div>
      <v-expansion-panels accordion>
        <v-expansion-panel
            v-for="arbre in textes" :key="arbre.id"
        >
          <v-expansion-panel-header>
            {{ arbre.name }}
          </v-expansion-panel-header>
          <v-expansion-panel-content>
            <v-card-text>
              <v-treeview
                  v-model="tree"
                  :items="arbre.children"
                  open-on-click
                  selection-type=""
                  return-object
              >
                <template v-slot:prepend="{ item }">
                  <v-btn @click="test(item)">
                  <v-icon v-if="item.children">
                    mdi-filter
                  </v-icon>
                  </v-btn>
                </template>
              </v-treeview>
            </v-card-text>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>
          <v-divider vertical></v-divider>
    <v-divider></v-divider>
  </div>
</template>
<script>
import axios from "axios";

export default {
  data: () => ({
    breweries: [],
    isLoading: false,
    tree: [],
    types: [],
    textes: []
  }),


  watch: {},
  mounted() {
    this.fetch()
  },
  methods: {
    test(item){
      this.$emit('fetch-parent', item);
    },
    emitFetchParent(parentData) {
      this.$emit('fetch-parent', parentData);
    },
    fetch() {
      return axios.get('/api/tree')
          .then(res => {
            return res.data;
          })
          .then(data => (this.textes = data))
          .catch(err => console.log(err))
    },
  },
}
</script>