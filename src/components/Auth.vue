<template>
    <div class="auth-container">
      <label for="username">Username</label><br>
      <input id="username" placeholder="username" v-model="username" /><br>
      <label for="password">Password</label><br>
      <input id="password" placeholder="password" v-model="password" type="password"/><br>
      <button @click="login()" v-if="loginMode">Login</button>
      <button @click="register()" v-else>Register</button>
      <p @click="loginMode = false" v-if="loginMode">Don't have an account yet? Register here.</p>
      <p @click="loginMode = true" v-else>You already have an account. Login here.</p>    
    </div>
</template>

<script>
export default {
    name: "Auth",
    data(){
        return {
            username: '',
            password: '',
            loginMode: true
        }
    },
    created(){
        if(this.$cookies.isKey('auth-token')){
            this.$router.push('/');
        }
    },
    methods: {
        login(){
            fetch(`http://127.0.0.1:8000/auth/`, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({username: this.username, password: this.password})
                })
                .then( resp => resp.json())
                .then( res => {
                    this.$cookies.set("auth-token", res.token, "30d");
                    this.$router.push('/');
                })
                .catch( error => console.log(error))
        },
        register(){
            fetch(`http://127.0.0.1:8000/api/users/`, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({username: this.username, password: this.password})
                })
                .then( () => {
                    this.login();
                })
                .catch( error => console.log(error))
        }
    }
}
</script>

<style scoped>
  .auth-container {
    width: 50%;
    margin: 50px 25%;
  }
  p {
    cursor: pointer;
  }
</style>