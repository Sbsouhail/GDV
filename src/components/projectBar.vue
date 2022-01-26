<template>
  <div class="project-bar">
    <div>
      <div
        @click="showVersions"
        class="header"
        @mouseover="this.arrow = true"
        @mouseout="this.arrow = false"
      >
        <div class="project-name">{{ this.projectData.name }}</div>
        <p>
          date: {{ this.fixedDate }} | versions:
          {{ this.projectData.versions.length }}
          <span class="arrow" v-if="this.arrow"
            ><i
              :class="{ 'fas fa-arrow-down': !show, 'fas fa-arrow-up': show }"
            ></i>
          </span>
        </p>
        <p>Downloads: {{ this.projectData.views }}</p>
      </div>
      <span class="trash" @click="removeProject">
        <i class="fas fa-trash me-2"></i>
      </span>
      <span
        class="eye"
        v-if="this.projectData.visible"
        @click.prevent="visibleOff"
      >
        <i class="fas fa-eye"></i>
      </span>
      <span class="eye" v-else @click.prevent="visibleOn">
        <i class="fas fa-eye-slash"></i>
      </span>
    </div>
  </div>
  <div class="versions-container" v-if="show">
    <input
      type="submit"
      class="btn btn-primary mb-2"
      value="Add Version"
      @click.prevent="showVersionForm"
    />
    <div class="versionForm" v-if="this.versionForm">
      <p v-if="error" style="color: red; font-size: 10px">Already Exists</p>
      <form>
        <div class="form-group">
          <label for="name">Version Name</label>
          <input
            autocomplete="off"
            v-model="this.versionName"
            type="text"
            class="form-control mb-2"
            id="name"
            placeholder="Enter name"
          />
        </div>
        <button
          type="submit"
          class="btn btn-primary me-2"
          @click.prevent="addVersion"
        >
          Submit
        </button>
        <button
          type="submit"
          class="btn btn-danger"
          @click.prevent="hideVersionForm"
        >
          Cancel
        </button>
      </form>
    </div>
    <version-bar
      v-for="version in versions"
      v-bind:key="version._id"
      :version="version"
      :project="projectData"
      :user="this.userId"
    ></version-bar>
  </div>
  <div id="2nd"></div>
</template>
<script>
import moment from "moment";
import versionBar from "./versionBar.vue";
import axios from "axios";
export default {
  components: {
    versionBar,
  },
  data() {
    return {
      versionName: null,
      versionForm: false,
      arrow: false,
      projectData: this.project,
      fixedDate: null,
      versions: null,
      show: false,
      userId: this.user,
      error: false,
    };
  },
  watch: {
    versionName: function (val) {
      if (this.contains(this.projectData.versions, "name", val)) {
        this.error = true;
      } else {
        this.error = false;
      }
    },
  },
  props: ["project", "user"],

  created: function () {
    this.fixedDate = moment(this.projectData.creationDate).format(
      "dddd, MMMM Do YYYY, h:mm:ss a"
    );
  },
  methods: {
    showVersions() {
      if (this.show) {
        this.show = false;
        this.versions = null;
      } else {
        this.show = true;
        this.versions = this.projectData.versions;
      }
    },
    visibleOff() {
      axios
        .patch(`http://localhost:3000/users/update/p/${this.userId}`, {
          _id: this.projectData._id,
          visible: false,
        })
        .then((response) => {
          this.projectData = response.data;
        })
        .catch((error) => {
          alert(error);
        });
    },
    visibleOn() {
      axios
        .patch(`http://localhost:3000/users/update/p/${this.userId}`, {
          _id: this.projectData._id,
          visible: true,
        })
        .then((response) => {
          this.projectData = response.data;
        })
        .catch((error) => {
          alert(error);
        });
    },
    contains(arr, key, val) {
      for (var i = 0; i < arr.length; i++) {
        if (arr[i][key] === val) return true;
      }
      return false;
    },
    goTo(elmnt) {
      let Y =
        window.scrollY +
        document.querySelector(elmnt).getBoundingClientRect().top;
      window.scrollTo(0, Y);
    },
    showVersionForm() {
      this.versionForm = true;
    },
    hideVersionForm() {
      this.versionForm = false;
    },
    removeProject() {
      let projectToRemove = {
        _id: this.projectData._id,
        name: this.projectData.name,
      };
      axios
        .patch(
          `http://localhost:3000/users/delete/p/${this.userId}`,
          projectToRemove
        )
        .then((response) => {
          this.$parent.projects = response.data.projects;
          this.$parent.$parent.user = response.data;
        })
        .catch((err) => {
          alert(err);
        });
    },
    addVersion() {
      if (
        this.versionName &&
        !this.contains(this.projectData.versions, "name", this.versionName)
      ) {
        let newVersion = {
          _id: `${this.projectData._id}/${this.versionName}`,
          name: this.versionName,
        };
        axios
          .patch(`http://localhost:3000/users/insert/v/${this.userId}`, {
            _id: this.projectData._id,
            version: newVersion,
          })
          .then((response) => {
            this.versions = response.data.project.versions;
            this.projectData = response.data.project;
            this.$parent.projects = response.data.user.projects;
          })
          .then(() => {
            this.versionForm = false;
          })
          .catch((err) => {
            alert(err);
          });
      }
    },
  },
};
</script>
<style scoped>
.project-bar {
  margin-bottom: 10px;
  padding: 20px;
  background-color: white;
  border-radius: 5px;
  border: 1px solid #dfe3e8;
  box-sizing: border-box;
  cursor: pointer;
  transition: all 0.25s ease;
}
.project-bar:hover {
  transform: scale(1.05, 1.05);
}
.project-name {
  font-weight: bold;
}
.versions-container {
  margin-left: 5%;
}
.trash:hover {
  font-size: 1.1em;
  color: red;
}
.eye:hover {
  font-size: 1.1em;
  color: blue;
}
.header {
  border-bottom: 1px solid gray;
}
.versionForm {
  margin-bottom: 10px;
  padding: 20px;
  background-color: white;
  border-radius: 5px;
  border: 1px solid #dfe3e8;
  box-sizing: border-box;
  cursor: pointer;
  transition: all 0.25s ease;
}
</style>