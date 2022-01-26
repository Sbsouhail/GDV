<template>
  <div class="test" id="home">
    <nav
      id="customNav"
      :class="[
        !this.$parent.$parent.myspace ? 'fixed-top' : '',
        'navbar navbar-expand-lg navbar-dark bg-dark',
      ]"
    >
      <div class="container-fluid">
        <a class="navbar-brand" @click.prevent="restart()"
          ><img class="logo" src="../assets/logo.jpg" alt=""
        /></a>
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarNavDropdown"
          aria-controls="navbarNavDropdown"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse underlined" id="navbarNavDropdown">
          <ul class="navbar-nav ms-auto">
            <li
              v-if="!this.$parent.$parent.myspace"
              class="nav-item me-2 pointer"
            >
              <a
                @click.prevent="goTo('#home')"
                class="nav-link active"
                aria-current="page"
                >Home</a
              >
            </li>
            <li class="nav-item me-2" v-if="!this.$parent.$parent.myspace">
              <a @click.prevent="goTo('#about')" class="nav-link active pointer"
                >About</a
              >
            </li>
            <li
              v-if="!this.$parent.$parent.myspace"
              class="nav-item me-2 pointer"
            >
              <a class="nav-link active" @click.prevent="goTo('#projects')"
                >Projects</a
              >
            </li>
            <li
              v-if="!this.$parent.$parent.myspace"
              class="nav-item me-2 pointer"
            >
              <a class="nav-link active" @click.prevent="goTo('#contact')"
                >Contact</a
              >
            </li>
            <li v-if="connected" class="nav-item dropdown me-2 dropstart">
              <a
                class="nav-link active"
                id="navbarDropdownMenuLink"
                role="button"
                data-bs-toggle="dropdown"
                aria-expanded="false"
              >
                <i class="fas fa-user"></i>
              </a>
              <ul
                class="dropdown-menu scroll"
                aria-labelledby="navbarDropdownMenuLink"
              >
                <li>
                  <a
                    v-if="!this.$parent.$parent.myspace"
                    class="dropdown-item pointer"
                    @click.prevent="goToSpace"
                    >My Space</a
                  >
                  <a
                    v-if="this.$parent.$parent.myspace"
                    class="dropdown-item pointer"
                    @click.prevent="goToHome"
                    >Home</a
                  >
                  <a
                    v-if="this.$parent.$parent.myspace"
                    class="dropdown-item pointer"
                    @click.prevent="deleteAccount"
                    >Delete Account</a
                  >
                </li>
                <li>
                  <a class="dropdown-item pointer" @click="logout">Logout</a>
                </li>
              </ul>
            </li>
            <li v-if="!connected" class="nav-item dropdown me-2 dropstart">
              <a
                @click="reset"
                class="nav-link active"
                id="navbarDropdownMenuLink"
                role="button"
                data-bs-toggle="dropdown"
                aria-expanded="false"
              >
                Login
              </a>
              <ul
                class="dropdown-menu scroll"
                aria-labelledby="navbarDropdownMenuLink"
              >
                <form-login
                  @submited="connect"
                  :userName="this.user"
                  class="dropdown-item Cdropdown-item"
                  v-if="login"
                />
                <form-inscription
                  class="dropdown-item Cdropdown-item"
                  v-if="inscription"
                />
              </ul>
            </li>
          </ul>
        </div>
      </div>
    </nav>
  </div>
</template>
<script>
import formLogin from "./formLogin.vue";
import formInscription from "./formInscription.vue";
import VueCookies from "vue-cookies";
import axios from "axios";
import CryptoJS from "crypto-js";

export default {
  components: {
    formLogin,
    formInscription,
  },
  data() {
    return {
      login: true,
      inscription: false,
      connected: null,
    };
  },
  methods: {
    deleteAccount() {
      axios
        .delete(`http://localhost:3000/users/${this.connected._id}`)
        .then(() => {
          this.restart();
        })
        .catch((err) => {
          alert(err);
        });
    },
    restart() {
      location.reload();
    },
    goTo(elmnt) {
      let Y =
        window.scrollY +
        document.querySelector(elmnt).getBoundingClientRect().top;
      window.scrollTo(0, Y);
    },
    goToHome() {
      this.$parent.$parent.myspace = false;
      this.refresh();
      this.goTo("#home");
    },
    goToSpace() {
      this.$parent.$parent.myspace = true;
      this.goTo("#home");
    },
    connect(value) {
      this.connected = value;
    },
    logout() {
      this.$parent.$parent.myspace = false;
      this.connected = null;
    },
    reset() {
      this.login = true;
      this.inscription = false;
    },
    changeFormType() {
      if (this.login) {
        this.login = false;
        this.inscription = true;
      } else {
        this.login = true;
        this.inscription = false;
      }
    },
    refresh() {
      if (VueCookies.get("username") && VueCookies.get("password")) {
        let bytes = CryptoJS.AES.decrypt(
          VueCookies.get("password"),
          "gdvencrypt01"
        );
        let originalPwd = bytes.toString(CryptoJS.enc.Utf8);
        axios
          .post(
            `http://localhost:3000/users/login/${VueCookies.get("username")}`,
            {
              password: originalPwd,
            }
          )
          .then((response) => {
            this.connected = response.data.user;
          })
          .catch(() => {
            this.logout;
          });
      }
    },
  },
  mounted: function () {
    window.scrollTo(0, 0);
  },
  watch: {
    connected: function () {
      this.$emit("gotUser", this.connected);
      if (this.connected == null) {
        VueCookies.remove("username");
        VueCookies.remove("password");
      } else {
        if (this.connected.remember) {
          VueCookies.set("username", this.connected._id, "30d");
          VueCookies.set("password", this.connected.password, "30d");
        }
      }
    },
  },
  created: function () {
    this.refresh();
  },
};
</script>
<style lang="scss" scoped>
#customNav {
  height: 75px;
  background-color: #1e2d3b !important;
  opacity: 0.9;
  .logo {
    height: 70px;
    border-radius: 50%;
    -moz-border-radius: 50%;
    -webkit-border-radius: 50%;
    width: 70px;
    cursor: pointer;
  }
  .pointer {
    cursor: pointer;
  }
  .underlined {
    a::after {
      content: "";
      width: 0%;
      height: 2px;
      background: #f44336;
      display: block;
      margin: auto;
      transition: 0.5s;
    }

    a:hover::after {
      width: 100%;
    }
  }
  .scroll {
    background-color: #e7e9eb;
    max-height: 97vh;
    overflow: auto;
    .Cdropdown-item {
      color: black;
      &:focus,
      &:hover {
        color: black;
        background-color: #e7e9eb;
      }
    }
  }
}
@media (max-width: 700px) {
  #customNav {
    height: auto;
    .scroll {
      max-height: 50vh;
    }
  }
}
</style>