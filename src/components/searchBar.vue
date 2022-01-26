<template>
  <div class="container-fluid customContainer">
    <a
      id="projects"
      style="display: block; position: relative; top: -80px; visibility: hidden"
    ></a>
    <p class="title">Search</p>
    <p>Search for Project</p>
    <div class="mb-3">
      <input
        autocomplete="off"
        v-model="search"
        required
        type="search"
        class="form-control"
      />
    </div>
    <div v-for="user in users" v-bind:key="user._id">
      <div class="users" v-if="filtered(user.projects).length > 0">
        <p class="user">{{ user.name }}:</p>
        <div
          class="project"
          v-for="project in filtered(user.projects)"
          v-bind:key="project._id"
        >
          {{ project.name }}
          <p class="v">
            (Versions:{{ project.versions.length }},Downloads:{{
              project.views
            }})
          </p>
          <div class="versions d-flex">
            <div
              @click="downloadVersion(version, user._id, project._id)"
              :class="{
                version: version.files.length > 0,
                versionHide: version.files.length == 0,
              }"
              v-for="version in project.versions"
              :key="version._id"
            >
              {{ version.name }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      users: [],
      search: "",
    };
  },
  methods: {
    downloadVersion(val, user, project) {
      axios
        .get("http://localhost:3000/users/download/v/" + user, {
          params: { version: val._id, versionName: val.name, project: project },
          responseType: "blob",
        })
        .then((response) => {
          var fileURL = window.URL.createObjectURL(new Blob([response.data]));
          var fileLink = document.createElement("a");
          fileLink.href = fileURL;
          fileLink.setAttribute("download", val.name + ".zip");
          document.body.appendChild(fileLink);
          fileLink.click();
        })
        .then(() =>
          axios.patch(`http://localhost:3000/users/Add/${user}`, {
            _id: project,
          })
        )
        .then((response) => {
          this.$parent.$parent.user = response.data;
          this.update(this.search);
        });
    },
    filtered(arr) {
      let newArray = [];
      for (var i = 0; i < arr.length; i++) {
        if (arr[i].name.includes(this.search) && arr[i].visible) {
          newArray.push(arr[i]);
        }
      }
      return newArray;
    },
    update(value) {
      this.users = [];
      axios
        .get("http://localhost:3000/users/many", {
          params: {
            word: value,
          },
        })
        .then((res) => {
          if (this.search != "") this.users = res.data;
        });
    },
  },
  watch: {
    search: function (value) {
      this.update(value);
    },
  },
};
</script>
<style lang="scss" scoped>
.customContainer {
  padding: 5% 30%;
  text-align: center;
  .title {
    margin-bottom: 40px;
    font-weight: bold;
    margin-bottom: 30px;
    font-size: 60px;
  }
  .users {
    margin-bottom: 10px;
    padding: 20px;
    background-color: white;
    border-radius: 5px;
    border: 1px solid #dfe3e8;
    box-sizing: border-box;
    text-align: left;
    .user {
      font-weight: bold;
    }
    .project {
      font-weight: bold;
      margin-bottom: 10px;
      padding: 20px;
      background-color: #e7e9eb;
      border-radius: 5px;
      border: 1px solid #dfe3e8;
      box-sizing: border-box;
      cursor: pointer;
      transition: all 0.25s ease;
      text-align: center;
      .v {
        font-size: 10px;
      }
      .version {
        color: white;
        background-color: #1e2d3b;
        padding: 1px 3px;
        border-radius: 10px;
        transition: all 0.25s ease;
      }
      .versionHide {
        display: none;
      }
      .version:hover {
        transform: scale(1.05, 1.05);
      }
    }
    .project:hover {
      transform: scale(1.05, 1.05);
    }
  }
}
@media (max-width: 700px) {
  .customContainer {
    padding: 5% 10%;
    .title {
      font-size: 40px !important;
    }
  }
}
</style>