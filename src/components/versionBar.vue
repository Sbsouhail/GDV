<template>
  <div class="version-bar">
    <div
      @click="showFiles"
      class="header"
      @mouseover="this.arrow = true"
      @mouseout="this.arrow = false"
    >
      <div class="version-name">{{ this.versionData.name }}</div>
      <p>
        {{ this.fixedDate }} | files: {{ this.versionData.files.length
        }}<span class="arrow" v-if="this.arrow"
          ><i
            :class="{ 'fas fa-arrow-down': !show, 'fas fa-arrow-up': show }"
          ></i>
        </span>
      </p>
    </div>

    <span
      v-if="this.versionData.files.length > 0"
      class="download"
      @click.prevent="downloadVersion"
    >
      <i class="fas fa-file-download"></i>
    </span>
  </div>

  <div class="files-container" v-if="show">
    <input
      type="submit"
      class="btn btn-primary mb-2"
      value="Add Files"
      @click.prevent="showFileForm"
    />
    <div class="fileForm" v-if="this.fileForm">
      <form enctype="multipart/form-data" @submit.prevent="sendFiles">
        <div class="mb-3">
          <label for="formFileMultiple" class="form-label">Files Upload</label>
          <input
            class="form-control"
            type="file"
            ref="files"
            @change="selectFile"
            id="formFileMultiple"
            multiple
          />
        </div>
        <button type="submit" class="btn btn-primary me-2">Submit</button>
        <button
          type="submit"
          class="btn btn-danger"
          @click.prevent="hideFileForm"
        >
          Cancel
        </button>
      </form>
    </div>
    <file-bar
      v-for="file in files"
      v-bind:key="file._id"
      :file="file"
      :version="versionData"
      :project="projectData"
      :user="userId"
    ></file-bar>
  </div>
</template>
<script>
import moment from "moment";
import fileBar from "./fileBar.vue";
import axios from "axios";
export default {
  components: { fileBar },
  data() {
    return {
      filesc: [],
      arrow: false,
      versionData: this.version,
      show: false,
      files: [],
      fixedDate: null,
      projectData: this.project,
      userId: this.user,
      fileForm: false,
    };
  },
  props: ["version", "project", "user"],
  created: function () {
    this.fixedDate = moment(this.versionData.creationDate).format(
      "dddd, MMMM Do YYYY, h:mm:ss a"
    );
  },
  methods: {
    downloadVersion() {
      axios
        .get("http://localhost:3000/users/download/v/" + this.userId, {
          params: {
            version: this.versionData._id,
            project: this.projectData._id,
            versionName: this.versionData.name,
          },
          responseType: "blob",
        })
        .then(async (response) => {
          var fileURL = window.URL.createObjectURL(new Blob([response.data]));

          var fileLink = document.createElement("a");

          fileLink.href = fileURL;

          fileLink.setAttribute("download", this.versionData.name + ".zip");

          document.body.appendChild(fileLink);

          fileLink.click();
        })
        .catch((err) => {
          alert(err);
        });
    },
    sendFiles() {
      const formData = new FormData();
      for (const i of Object.keys(this.filesc)) {
        formData.append("files", this.filesc[i]);
      }
      formData.append("version", this.versionData._id);
      formData.append("project", this.projectData._id);
      console.log(formData);
      axios
        .patch(`http://localhost:3000/users/upload/${this.userId}`, formData)
        .then((response) => {
          this.versionData = response.data.version;
          this.projectData = response.data.project;
          this.files = response.data.version.files;

          this.$parent.projectData = response.data.project;
          this.fileForm = false;
        })
        .catch((err) => alert(err));
    },
    selectFile() {
      const filesc = this.$refs.files.files;
      this.filesc = filesc;
    },
    showFiles() {
      if (this.show) {
        this.show = false;
      } else {
        this.show = true;
        this.files = this.versionData.files;
      }
    },
    removeVersion() {
      let versionToRemove = {
        _id: this.versionData._id,
      };
      axios
        .patch(`http://localhost:3000/users/delete/v/${this.userId}`, {
          _id: this.projectData._id,
          version: versionToRemove,
        })
        .then((response) => {
          this.$parent.$parent.projects = response.data.user.projects;
          this.$parent.projectData = response.data.project;
          this.$parent.versions = response.data.project.versions;
        })
        .catch((err) => {
          alert(err);
        });
    },
    showFileForm() {
      this.fileForm = true;
    },
    hideFileForm() {
      this.fileForm = false;
    },
  },
};
</script>
<style scoped>
.version-bar {
  margin-bottom: 10px;
  padding: 20px;
  background-color: white;
  border-radius: 5px;
  border: 1px solid #dfe3e8;
  box-sizing: border-box;
  cursor: pointer;
  transition: all 0.25s ease;
}
.version-bar:hover {
  transform: scale(1.05, 1.05);
}
.files-container {
  margin-left: 5%;
}
.version-name {
  font-weight: bold;
}
.trash:hover {
  font-size: 1.1em;
  color: red;
}
.header {
  border-bottom: 1px solid gray;
}
.fileForm {
  margin-bottom: 10px;
  padding: 20px;
  background-color: white;
  border-radius: 5px;
  border: 1px solid #dfe3e8;
  box-sizing: border-box;
  cursor: pointer;
  transition: all 0.25s ease;
}
.download:hover {
  font-size: 1.1em;
  color: blue;
}
</style>