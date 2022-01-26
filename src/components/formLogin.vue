<template>
  <form @submit.prevent="connect">
    <h4>Login</h4>
    <div class="mb-3">
      <label for="userName" class="form-label">User Name:</label>
      <input
        autocomplete="off"
        required
        type="text"
        class="form-control"
        id="userName"
        v-model="username"
      />
      <p class="err" v-if="this.messageLogin">{{ this.messageLogin }}</p>
    </div>
    <div class="mb-3">
      <label for="pwd" class="form-label">Password</label>
      <input
        autocomplete="off"
        type="password"
        class="form-control"
        id="pwd"
        required
        v-model="password"
      />
      <p class="err" v-if="this.messagePwd">{{ this.messagePwd }}</p>
    </div>
    <div class="mb-3 form-check">
      <input
        type="checkbox"
        class="form-check-input"
        id="chck"
        v-model="remember"
      />
      <label class="form-check-label" for="chck">Remember me</label>
    </div>
    <input type="submit" class="btn btn-primary me-2" value="submit" />
    <div
      v-if="this.loading"
      class="spinner-border spinner-border-sm"
      role="status"
    >
      <span class="visually-hidden">Loading...</span>
    </div>
    <br />
    <change-form :message="messageText" />
  </form>
</template>
<script>
import changeForm from "./changeFormType.vue";
import axios from "axios";
import CryptoJS from "crypto-js";
export default {
  components: {
    changeForm,
  },
  data() {
    return {
      loading: false,
      messageLogin: null,
      messagePwd: null,
      username: this.userName,
      password: null,
      messageText: "You Don't have an Account ?",
      remember: false,
    };
  },
  props: ["userName"],
  methods: {
    setMessage(msg, status) {
      if (status == 404) {
        this.messageLogin = msg;
        this.messagePwd = null;
      } else {
        this.messagePwd = msg;
        this.messageLogin = null;
      }
    },
    connect() {
      this.loading = true;
      this.setMessage(null, null);
      axios
        .post(`http://localhost:3000/users/login/${this.username}`, {
          password: this.password,
        })
        .then((response) => {
          let user = response.data.user;
          let cryptedPwd = CryptoJS.AES.encrypt(
            this.password,
            "gdvencrypt01"
          ).toString();
          user.password = cryptedPwd;
          user.remember = this.remember;
          this.$emit("submited", user);
        })
        .catch((error) => {
          this.$emit("submited", null);
          if (error.response) {
            let msg = error.response.data.message;
            this.loading = false;
            this.setMessage(msg, error.response.status);
          }
        });
    },
  },
};
</script>
<style lang="scss" scoped>
.err {
  color: red;
}
</style>