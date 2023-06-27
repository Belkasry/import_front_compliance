<template>
  <v-app-bar flat>
    <v-app-bar-title>
      <v-row>

      </v-row>
    </v-app-bar-title>
  </v-app-bar>
</template>

<script>
//create function submitMap to send data to backend in methods
import axios from "axios";
export default {
  components: {},
  data() {
    return {}
  },
  methods: {
    async submitMap() {
      const formData = new FormData();
      // formData.append("mapping", JSON.stringify(this.columnMappings));
      // formData.append("mapping_fige", JSON.stringify(this.columnMappingsFige));
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
          this.$emit('reload', "reload");
        })
        .catch((error) => {
          console.log(error);
        });
    }
  }}
</script>
