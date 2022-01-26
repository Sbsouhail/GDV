<template>
  <div>
    <about-section />
    <div class="seperator"></div>
    <stats-section />
    <div class="seperator"></div>
    <search-bar />
    <div class="seperator"></div>
    <contact-section />
  </div>
</template>
<script>
import statsSection from "./statsSection.vue";
import contactSection from "./contactSection.vue";
import aboutSection from "./aboutSection.vue";
import searchBar from "./searchBar.vue";
export default {
  name: "App",
  components: { aboutSection, searchBar, contactSection, statsSection },
};
</script>
<style lang="scss" scoped>
.seperator {
  border-radius: 25px;
  content: "";
  width: 70%;
  height: 2px;
  background: #f44336;
  display: block;
  margin: auto;
  margin-top: 50px;
  margin-bottom: 50px;
}
</style>