<template>
  <a class="logLink" @click.stop="changeLoginState">{{ message }}</a>
</template>
<script>
export default {
  methods: {
    changeLoginState() {
      this.$parent.$parent.changeFormType();
    },
  },
  props: ["message"],
};
</script>
<style lang="scss" scoped>
.logLink {
  cursor: pointer;
}
</style>