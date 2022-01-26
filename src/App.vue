<template>
  <div>
    <header-c @gotUser="getuser" /> <body-section v-if="!myspace" /><my-space
      v-if="myspace"
      :userData="this.user"
    ></my-space
    ><footer-s v-if="!myspace" />
  </div>
</template>

<script>
import bodySection from "./components/bodySection.vue";
import HeaderC from "./components/HeaderC.vue";
import footerS from "./components/footerSection.vue";
import mySpace from "./components/mySpace.vue";

export default {
  name: "App",
  components: { HeaderC, bodySection, mySpace, footerS },
  data() {
    return {
      user: null,
      myspace: false,
    };
  },
  methods: {
    getuser(val) {
      this.user = val;
    },
  },
};
</script>

<style lang="scss">
#app {
  @import url("https://fonts.googleapis.com/css2?family=Poppins:wght@100;200;300;400;600;700&display=swap");
  font-family: "Poppins", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  scroll-behavior: smooth;
  line-height: 1.6;
}
</style>
