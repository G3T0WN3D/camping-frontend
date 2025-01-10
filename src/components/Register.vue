<template>
    <div>
      <h1>Registreren</h1>
      <form @submit.prevent="register">
        <input type="text" placeholder="Gebruikersnaam" v-model="username" />
        <input type="email" placeholder="E-mailadres" v-model="email" />
        <input type="password" placeholder="Wachtwoord" v-model="password" />
        <button type="submit">Registreer</button>
      </form>
    </div>
  </template>
  
  <script>
  export default {
    name: 'RegisterPage',
    data() {
      return {
        username: '',
        email: '',
        password: '',
      };
    },
    methods: {
      async register() {
        if (!this.username || !this.email || !this.password) {
          alert('Alle velden zijn verplicht.');
          return;
        }
  
        try {
          const response = await fetch('http://localhost:3000/register', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({
              username: this.username,
              email: this.email,
              password: this.password,
            }),
          });
  
          const data = await response.json();
  
          if (response.ok) {
            alert('Registratie succesvol!');
          } else {
            alert(`Fout: ${data.message}`);
          }
        } catch (error) {
          console.error(error);
          alert('Er is een fout opgetreden.');
        }
      },
    },
  };
  </script>
  