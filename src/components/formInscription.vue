<template>
  <form @submit.prevent="signup">
    <h4>Inscription:</h4>
    <div class="mb-3">
      <label for="name" class="form-label">First Name:</label>
      <input
        autocomplete="off"
        required
        type="text"
        class="form-control"
        id="name"
        v-model="name"
      />
    </div>
    <div class="mb-3">
      <label for="lastname" class="form-label">Last Name:</label>
      <input
        autocomplete="off"
        required
        type="text"
        class="form-control"
        id="lastname"
        v-model="lastname"
      />
    </div>
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
      <p v-if="this.userConflict" class="err">{{ this.userConflict }}</p>
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
    </div>
    <div class="mb-3">
      <label for="email" class="form-label">Email address:</label>
      <input
        autocomplete="off"
        type="email"
        class="form-control"
        id="email"
        aria-describedby="emailHelp"
        v-model="email"
      />
      <div id="emailHelp" class="form-text">
        We'll never share your email with anyone else.
      </div>
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
export default {
  components: {
    changeForm,
  },
  data() {
    return {
      userConflict: null,
      loading: false,
      username: null,
      name: null,
      lastname: null,
      email: null,
      password: null,
      messageText: "You Have an Account ?",
    };
  },
  methods: {
    reset() {
      this.username = null;
      this.name = null;
      this.lastname = null;
      this.email = null;
      this.password = null;
    },
    signup() {
      this.loading = true;
      axios
        .post("http://localhost:3000/users", {
          _id: this.username,
          name: this.name,
          lastname: this.lastname,
          email: this.email,
          password: this.password,
        })
        .then(() => {
          this.reset();
          this.loading = false;
          this.$parent.changeFormType();
        })
        .catch((error) => {
          if (error.response) {
            if (error.response.status == 409) {
              this.userConflict = error.response.data.message;
              this.loading = false;
            }
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
