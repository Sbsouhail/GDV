<template>
  <div class="container-fluid customC">
    <a
      id="contact"
      style="display: block; position: relative; top: -90px; visibility: hidden"
    ></a>
    <form ref="form" @submit.prevent="sendEmail">
      <div class="smallC"><p>Contact</p></div>
      <div class="mb-3">
        <label for="userName" class="form-label">First Name:</label>
        <input
          name="from_name"
          type="text"
          class="form-control"
          id="userName"
          v-model="name"
        />
      </div>
      <div class="mb-3">
        <label for="userName" class="form-label">Last Name:</label>
        <input
          name="from_lname"
          required
          type="text"
          class="form-control"
          id="userName"
          v-model="lastname"
        />
      </div>

      <div class="mb-3">
        <label for="email" class="form-label">Email address:</label>
        <input
          type="email"
          name="from_email"
          class="form-control"
          id="email"
          aria-describedby="emailHelp"
          v-model="email"
        />
        <div id="emailHelp" class="form-text">
          We'll never share your email with anyone else.
        </div>
      </div>
      <div class="mb-3">
        <label for="txt" class="form-label">leave a message:</label>
        <textarea
          name="message"
          required
          class="form-control"
          id="txt"
          rows="4"
          v-model="message"
        />
      </div>
      <input type="submit" class="btn btn-primary" value="submit" />
      <br />
      <change-form :message="messageText" />
    </form>
  </div>
</template>
<script>
import emailjs from "@emailjs/browser";
export default {
  data() {
    return {
      name: null,
      lastname: null,
      email: null,
      message: null,
    };
  },
  methods: {
    sendEmail() {
      emailjs
        .sendForm(
          "service_ye3bxbj",
          "template_gktreuk",
          this.$refs.form,
          "user_1eLuhJyebp7SAqED3052c"
        )
        .then(
          (result) => {
            alert("SUCCESS!", result.text);
            this.name = null;
            this.lastname = null;
            this.email = null;
            this.message = null;
          },
          (error) => {
            console.log("FAILED...", error.text);
          }
        );
    },
  },
};
</script>
<style lang="scss" scoped>
.customC {
  padding: 5% 20%;
  .smallC {
    text-align: center;
    margin-bottom: 40px;
    p {
      font-weight: bold;
      font-size: 60px;
    }
  }
}
@media (max-width: 700px) {
  p {
    font-size: 40px !important;
  }
}
</style>