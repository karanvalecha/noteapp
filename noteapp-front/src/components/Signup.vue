<template>
  <div class="container">
    <div>
      <h3>Sign Up</h3>
      <form @submit.prevent="signup">
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
        <!-- Password Confirmation -->
        <div class="form-group">
          <label for="password_confirmation">Confirm Password: </label>
          <input type="password" v-model="password_confirmation" id="password_confirmation" placeholder="" class="form-control">
        </div>
        <button type="submit" class="font-sans font-bold btn btn-primary">Sign Un</button>
        <div class="">
          <router-link to="/">Sign In</router-link>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import $ from 'jquery'

export default {
  name: 'Signup',
  data () {
    return {
      email: '',
      password: '',
      password_confirmation: '',
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
        let payload = {
          email: this.email,
          password: this.password,
          password_confirmation: this.password_confirmation
        };
        let context = this;
        $.ajax({
          url: "http://localhost:3000/signup",
          method: "POST",
          data: JSON.stringify(payload),
          success(data) {
            console.log("Register API success.")
            context.signinSuccessful(data);
          }, 
          error(error) {
            console.log("Register API failure.")
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
      this.error = (error.response && error.response.data && error.response.data.error) || "Something went wrong"
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