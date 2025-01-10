<template>
    <div>
      <h1>Inloggen</h1>
      <form @submit.prevent="login">
        <input
          type="text"
          placeholder="Gebruikersnaam"
          v-model="username"
          required
        />
        <input
          type="password"
          placeholder="Wachtwoord"
          v-model="password"
          required
        />
        <button type="submit">Log in</button>
      </form>
      <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
    </div>
  </template>
  
  <script>
  export default {
    name: "LoginPage",
    data() {
      return {
        username: "",
        password: "",
        errorMessage: "",
      };
    },
    methods: {
        async login() {
            if (!this.username || !this.password) {
                this.errorMessage = 'Alle velden zijn verplicht.';
                return;
            }

            try {
                const response = await fetch('http://localhost:3000/login', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({
                    username: this.username,
                    password: this.password,
                }),
                });

                const data = await response.json();

                if (response.ok) {
                this.$emit('loginSuccess', data.userId); // Stuur userId mee naar de parent
                } else {
                this.errorMessage = data.message;
                }
            } catch (error) {
                console.error(error);
                this.errorMessage = 'Er is iets misgegaan bij het inloggen.';
            }
        },
    },
  };
  </script>
  
  <style>
  .error {
    color: red;
    margin-top: 10px;
  }
  </style>
  