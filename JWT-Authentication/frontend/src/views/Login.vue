<template>
  <div class="Login">
    <div class="row">
      <div class="col-lg-4 mx-auto border shadow rounded p-4 mt-3">
        <h1 class="text-center mt-3 mb-4">Login</h1>
        <form @submit.prevent="doLogin">
          <div class="form-group">
            <label for="usernameInput">Username</label>
            <input
              type="text"
              class="form-control"
              id="usernameInput"
              v-model="username"
              :class="{
                'is-invalid':usernameE===true,
                'is-valid':usernameE===false,
             }">
             <div class="invalid-feedback" v-if="usernameE">
              {{usernameEM}}
             </div>
          </div>
          <div class="form-group">
            <label for="passwordInput">Password</label>
            <input
              type="password"
              class="form-control"
              id="passwordInput"
              v-model="password"
              :class="{
                'is-invalid':passwordE===true,
                'is-valid':passwordE===false,
             }">
             <div class="invalid-feedback" v-if="passwordE">
              {{passwordEM}}
             </div>
          </div>
          <button type="submit" class="btn btn-primary">Login</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Login',
  data() {
      return {
        username: '',
        password: '',
        usernameE: null,
        passwordE: null,
        usernameEM: null,
        passwordEM: null,
      }
  },
  methods: {
    doLogin() {
      let access = true

      if (this.username.length < 5) {
        access = false
        this.usernameE = true
        if (this.username.length == 0) {
          this.usernameEM = "Username required."
        } else {
          this.usernameEM = "Username must be at least 5 characters long."
        }
      } else {
        this.usernameE = false
        this.usernameEM = ''
      }

      if (this.password.length < 8) {
        access = false
        this.passwordE = true
        if (this.password.length == 0) {
          this.passwordEM = "Password required."
        } else {
          this.passwordEM = "Password must be at least 8 characters long."
        }
      } else {
        this.passwordE = false
        this.passwordEM = ''
      }
      
      if (access) {
        axios
          .post('/api/dj-rest-auth/login/', {
            username: this.username,
            password: this.password,
            email: ""
          })
          .then(response => {
            let token = response.data.access_token
            let refresh = response.data.refresh_token
            // console.log(access_token)
            // console.log(refresh_token)
            this.$store.state.user_id = response.data.user.pk
            this.$store.commit("login", {token, refresh})
            this.$router.push("/profile")
          })
          .catch(error => {
            // console.log(error.response.data.non_field_errors.join(" "))
            // this.usernameEM = error.response.data.non_field_errors.join(" ")
            // console.log(error.response.data)
            this.usernameE = true
            this.passwordE = true
          })
      }
    }
  }
}
</script>