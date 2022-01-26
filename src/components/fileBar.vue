<template>
  <div class="file-bar">
    <div class="header">
      <div class="file-name">
        {{ this.fileData.name }}
      </div>
      <p v-if="this.versionData.name != this.fileData.from">
        ({{ this.fileData.from }})
      </p>
      <p>{{ this.fixedDate }}</p>
    </div>
    <span class="download" @click.prevent="downloadFile">
      <i class="fas fa-file-download"></i>
    </span>
  </div>
</template>
<script>
import moment from "moment";
import axios from "axios";
export default {
  data() {
    return {
      fileData: this.file,
      fixedDate: null,
      versionData: this.version,
      projectData: this.project,
      userId: this.user,
    };
  },
  props: ["file", "version", "project", "user"],
  created: function () {
    this.fixedDate = moment(this.fileData.creationDate).format(
      "dddd, MMMM Do YYYY, h:mm:ss a"
    );
  },
  methods: {
    removeFile() {
      let fileToRemove = {
        _id: this.fileData._id,
      };
      axios
        .patch(`http://localhost:3000/users/delete/f/${this.userId}`, {
          _id: this.projectData._id,
          version: this.versionData,
          file: fileToRemove,
        })
        .then((response) => {
          this.$parent.$parent.$parent.projects = response.data.user.projects;
          this.$parent.$parent.projectData = response.data.project;
          this.$parent.$parent.versions = response.data.project.versions;
          this.$parent.files = response.data.files;
          this.$parent.versionData = response.data.version;
        })
        .catch((err) => {
          alert(err.message);
        });
    },
    downloadFile() {
      axios
        .get("http://localhost:3000/users/download", {
          params: { _id: this.fileData._id, name: this.fileData.name },
          responseType: "blob",
        })
        .then((response) => {
          var fileURL = window.URL.createObjectURL(new Blob([response.data]));

          var fileLink = document.createElement("a");

          fileLink.href = fileURL;

          fileLink.setAttribute("download", this.fileData.name);

          document.body.appendChild(fileLink);

          fileLink.click();
        });
    },
  },
};
</script>
<style scoped>
.file-bar {
  margin-bottom: 10px;
  padding: 20px;
  background-color: white;
  border-radius: 5px;
  border: 1px solid #dfe3e8;
  box-sizing: border-box;
  cursor: pointer;
  transition: all 0.25s ease;
}
.file-bar:hover {
  transform: scale(1.05, 1.05);
}
.file-name {
  font-weight: bold;
}
.header {
  border-bottom: 1px solid gray;
}
.trash:hover {
  font-size: 1.1em;
  color: red;
}
.download:hover {
  font-size: 1.1em;
  color: blue;
}
</style>