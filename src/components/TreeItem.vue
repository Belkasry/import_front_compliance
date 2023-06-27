<template>

  <v-expansion-panels variant="accordion">
    <v-expansion-panel >
      <v-expansion-panel-header>
        <v-btn @click="fetchParent(model)" color="info" class="mr-2" size="small">
          <v-icon icon="mdi-view-list" color="white"></v-icon>
        </v-btn>
       {{ model.name }}
      </v-expansion-panel-header>
      <v-expansion-panel-content>
        <template v-for="model in model.children" >
          <div :key="model.id">
          <TreeItem
            v-if="model.children.length>0"
            class="item"
            :model="model"
            @fetch-parent="emitFetchParent"
          >
          </TreeItem>
          <v-list-item
            v-else
            prepend-icon="mdi-view-list"
            :title="model.name"
          >
          </v-list-item>
          </div>
        </template>


      </v-expansion-panel-content>
    </v-expansion-panel>
  </v-expansion-panels>
</template>
<script>
export default {
  name: 'TreeItem', // necessary for self-reference
  props: {
    model: Object,

  },
  data() {
    return {
    }
  },
  computed: {
  },
  methods: {
    fetchParent(model) {
        this.$emit('fetch-parent', model);
    },
    emitFetchParent(parentData) {
      this.$emit('fetch-parent', parentData);
    },
    toggle() {
    },
    changeType() {
    },
    addChild() {
    }
  }
}
</script>
<style>
.v-expansion-panel__shadow {
  box-shadow: unset !important;
}
.v-expansion-panel-text__wrapper {
  padding-left: 15px;
}
</style>
