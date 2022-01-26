<template>
  <div>
    <div id="home"><nav-bar @gotUser="getuser" /></div>
    <div v-if="!this.$parent.myspace" class="banner">
      <div class="text-box">
        <h1 text-center>VERSIONS MANAGER</h1>
      </div>
    </div>
  </div>
</template>
<script>
import navBar from "./navBar.vue";
export default {
  components: {
    navBar,
  },
  data() {
    return {
      user: null,
    };
  },
  methods: {
    getuser(val) {
      this.user = val;
    },
  },
  watch: {
    user: function () {
      this.$emit("gotUser", this.user);
    },
  },
};
</script>
<style lang="scss" scoped>
.banner {
  .text-box {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
    h1 {
      color: #fff;
      font-size: 62px;
    }
  }
  min-height: 85vh;
  background-image: linear-gradient(
      rgba(4, 9, 30, 0.555),
      rgba(4, 9, 30, 0.555)
    ),
    url(../assets/bannertech.jpg);
  background-position: center;
  background-size: cover;
}
@media (max-width: 700px) {
  .banner {
    .text-box {
      h1 {
        font-size: 20px;
      }
    }
  }
}
</style>