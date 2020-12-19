<template>
  <b-navbar>
    <template slot="brand">
      <b-navbar-item tag="router-link" :to="{ path: '/' }">
        <img
          src="https://raw.githubusercontent.com/buefy/buefy/dev/static/img/buefy-logo.png"
          alt="Lightweight UI components for Vue.js based on Bulma"
        />
      </b-navbar-item>
    </template>
    <template slot="start">
      <b-navbar-item href="#">
        Home
      </b-navbar-item>
    </template>

    <template slot="end">
      <b-navbar-item tag="div" v-if="!loggedin">
        <div class="buttons">
          <router-link to="/signup">
            <a class="button is-primary">
              <strong>Sign up</strong>
            </a>
          </router-link>
          <router-link to="/login">
            <a class="button is-light">
              Log in
            </a>
          </router-link>
        </div>
      </b-navbar-item>
      <b-navbar-item @click="logout" v-else>
        Logout
      </b-navbar-item>
    </template>
  </b-navbar>
</template>
<script>
import {removeToken} from '../utils/auth'
export default {
  data() {
    return {};
  },
  computed: {
    loggedin() {
      return localStorage.getItem("loggedin")
    },
  },
  methods: {
    async logout() {
      await this.$axios
        .post('auth/logout')
        .then((res) => {
            removeToken()
           this.$router.push('/')
        })
        .catch((error) => {
          Vue.$toast.open({
            message: "Server error",
            type: "error",
          });
        });

    },
  },
};
</script>

<style></style>
