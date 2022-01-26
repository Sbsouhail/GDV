<template>
  <div class="Space">
    <input
      type="submit"
      class="btn btn-success mb-5"
      value="Add Project"
      @click.prevent="showProjectForm"
    />
    <div class="projectForm" v-if="this.projectForm">
      <p v-if="error" style="color: red; font-size: 10px">Already Exists</p>

      <form>
        <div class="form-group">
          <label for="name">Project Name</label>
          <input
            autocomplete="off"
            v-model="this.projectName"
            type="text"
            class="form-control mb-2"
            id="name"
            placeholder="Enter name"
          />
        </div>
        <button
          type="submit"
          class="btn btn-primary me-2"
          @click.prevent="addProject"
        >
          Submit
        </button>
        <button
          type="submit"
          class="btn btn-danger"
          @click.prevent="hideProjectForm"
        >
          Cancel
        </button>
      </form>
    </div>
    <project-bar
      v-for="project in projects"
      v-bind:key="project._id"
      :project="project"
      :user="this.userData._id"
    ></project-bar>
  </div>
  <div id="projectForm"></div>
</template>
<script>
import projectBar from "./projectBar.vue";
import axios from "axios";
export default {
  components: { projectBar },
  data() {
    return {
      error: false,
      projectName: null,
      projectForm: false,
      projects: this.userData.projects,
    };
  },
  watch: {
    projectName: function (val) {
      if (this.contains(this.projects, "name", val)) {
        this.error = true;
      } else {
        this.error = false;
      }
    },
  },
  props: ["userData"],
  methods: {
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
    showProjectForm() {
      this.projectForm = true;
    },
    hideProjectForm() {
      this.projectForm = false;
    },
    addProject() {
      if (
        this.projectName &&
        !this.contains(this.userData.projects, "name", this.projectName)
      ) {
        let newProject = {
          _id: `${this.userData._id}/${this.projectName}`,
          name: this.projectName,
        };
        axios
          .patch(
            `http://localhost:3000/users/insert/p/${this.userData._id}`,
            newProject
          )
          .then((response) => {
            this.projects = response.data.projects;
          })
          .catch((err) => {
            alert(err);
          })
          .then(() => {
            this.projectForm = false;
            this.goTo("#projectForm");
          });
      }
    },
  },
};
</script>
<style lang="scss" scoped>
.Space {
  padding: 30px 70px;
  .projectForm {
    margin-bottom: 10px;
    padding: 20px;
    background-color: white;
    border-radius: 5px;
    border: 1px solid #dfe3e8;
    box-sizing: border-box;
    cursor: pointer;
    transition: all 0.25s ease;
  }
}
</style>