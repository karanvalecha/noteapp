<template>
  <div class="container">
    <div>
      <h3>Sign In</h3>
      <form @submit.prevent="signin">
        <!-- Error Div -->
        <div v-if="error">{{ error }}</div>
        <!-- Email -->
        <div class="form-group">
          <label for="email">Email: </label>
          <input type="email" v-model="email" id="email" placeholder="example@example.org" class="form-control">
        </div>
        <!-- Password -->
        <div class="form-group">
          <label for="password">Password: </label>
          <input type="password" v-model="password" id="password" placeholder="" class="form-control">
        </div>
        <button type="submit" class="font-sans font-bold btn btn-primary">Sign In</button>
        <div class="">
          <router-link to="/signup">Sign Up</router-link>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import $ from 'jquery'

export default {
  name: 'Signin',
  data () {
    return {
      email: '',
      password: '',
      error: ''
    }
  },
  created () {
    this.checkSignIn()
  },
  updated() {
    this.checkSignIn()
  },
  methods: {
    signin() {
      /*this.$http.plain.post('/signin', {email: this.email, password: this.password})
        .then(response => this.signinSuccessful(response))
        .catch(error => this.signinFailed(error))*/
        let payload = {
          email: this.email,
          password: this.password
        };
        let context = this;
        $.ajax({
          url: "http://localhost:3000/signin",
          method: "POST",
          data: JSON.stringify(payload),
          success(data) {
            console.log("Login API success.")
            context.signinSuccessful(data);
          }, 
          error(error) {
            console.log("Login API failure.")
            context.signinFailed(error);
          }
        })
    },
    signinSuccessful(response) {
      if(!response.data.csrf) {
        this.signinFailed(response)
        return
      }
      localStorage.csrf = response.data.csrf
      localStorage.signedIn = true
      this.error = ''
      this.$router.replace('/notes')
    },
    signinFailed(error) {
      this.error = (error.response && error.response.data && error.response.data.error) || ""
      delete localStorage.csrf
      delete localStorage.signedIn
    },
    checkSignIn() {
      if(localStorage.signedIn) {
        this.$router.replace('/notes')
      }
    }
  }
}
</script>

<style scoped>
</style>