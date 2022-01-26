<template>
  <div class="container-fluid">
    <div class="row row-cols-1 row-cols-md-3 g-4 justify-content-center">
      <div class="col" style="width: 15rem; height: 20rem">
        <div class="card test">
          <img src="../assets/stats.jpg" class="card-img-top" alt="..." />
          <div class="card-body" style="background-color: #1e2d3b">
            <h5 class="card-title">{{ users }}</h5>
            <p class="card-text">
              Number of total users who joined our great platform.
            </p>
          </div>
        </div>
      </div>
      <div class="col me-5 ms-5" style="width: 15rem; height: 20rem">
        <div class="card test">
          <img src="../assets/stats.jpg" class="card-img-top" alt="..." />
          <div class="card-body" style="background-color: #1e2d3b">
            <h5 class="card-title">{{ projects }}</h5>
            <p class="card-text">Number of total projects in our platform.</p>
          </div>
        </div>
      </div>
      <div class="col" style="width: 15rem; height: 20rem">
        <div class="card test">
          <img src="../assets/stats.jpg" class="card-img-top" alt="..." />
          <div class="card-body" style="background-color: #1e2d3b">
            <h5 class="card-title">{{ views }}</h5>
            <p class="card-text">Number of total Downloads</p>
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
    return { views: 0, projects: 0, users: 0 };
  },
  created: function () {
    this.refreshStats();
  },
  methods: {
    refreshStats() {
      axios.get("http://localhost:3000/users/n").then((res) => {
        this.users = res.data;
      });
      axios.get("http://localhost:3000/users/n/p").then((res) => {
        this.projects = res.data;
      });
      axios.get("http://localhost:3000/users/n/d").then((res) => {
        this.views = res.data;
      });
    },
  },
};
</script>
<style lang="scss" scoped>
.test {
  height: 18rem;
  color: white;
}
</style>