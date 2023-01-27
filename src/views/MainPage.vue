<template>
  <div>
    <input v-model="username" type="text" placeholder="username">
    <input v-model="email" type="text" placeholder="email">
    <input v-model="password" type="password" placeholder="password">
    <button v-on:click="signin()">Sign In</button>
    <button v-on:click="login()">Log In</button>
    <form @submit.prevent="uploadFile">
      <div>
        <label for="file">Choose file to upload</label>
        <input name="movies" type="file" accept=".txt" @change="onFileUpload">
      </div>
      <div>
        <button >Submit</button>
      </div>
    </form>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      username: "",
      email: "",
      password: "",

      FILE: null
    }
  },
  methods: {
    async signin() {
      const signinRes = await axios.post("http://localhost:3000/user/signin", {}, {
        headers: {
          "Content-Type": "application/json",
          "user-name": this.username,
          "email": this.email,
          "password": this.password
        }
      });

      console.log(signinRes);
    },
    async login() {
      const loginRes = await axios.post("http://localhost:3000/user/login", {}, {
        headers: {
          "Content-Type": "application/json",
          "email": this.email,
          "password": this.password
        }
      });

      localStorage.setItem("authorization", loginRes.data.token);
    },
    onFileUpload (event) {
      this.FILE = event.target.files[0]
    },
    async uploadFile() {
      const formData = new FormData()
      formData.append("movies", this.FILE, this.FILE.name)
      axios.post("http://localhost:3000/importMovieFromFile", formData, {
        headers: {
          "authorization": `Bearer ${localStorage.getItem("authorization")}`
        }
      }).then((res) => {
        console.log(res)
      })
    }
  }
}
</script>