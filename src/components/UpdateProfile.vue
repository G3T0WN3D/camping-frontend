<template>
    <div class="update-profile">
      <h1>Update Profiel</h1>
      <form @submit.prevent="updateProfile">
        <div>
          <label for="username">Nieuwe gebruikersnaam:</label>
          <input
            type="text"
            id="username"
            v-model="updatedUsername"
            placeholder="Voer nieuwe gebruikersnaam in"
          />
        </div>
        <div>
          <label for="password">Nieuw wachtwoord:</label>
          <input
            type="password"
            id="password"
            v-model="updatedPassword"
            placeholder="Voer nieuw wachtwoord in"
          />
        </div>
        <button type="submit">Update</button>
      </form>
      <p v-if="successMessage" class="success">{{ successMessage }}</p>
      <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
    </div>
  </template>
  
  <script>
  export default {
    name: "UpdateProfile",
    props: {
      userId: {
        type: Number,
        required: true,
      },
    },
    data() {
      return {
        updatedUsername: "",
        updatedPassword: "",
        successMessage: "",
        errorMessage: "",
      };
    },
    methods: {
      async updateProfile() {
        if (!this.userId) {
          this.errorMessage = "Gebruiker is niet ingelogd.";
          return;
        }
  
        const payload = {
          userId: this.userId,
          username: this.updatedUsername,
          password: this.updatedPassword,
        };
  
        try {
          const response = await fetch("http://localhost:3000/update-profile", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify(payload),
          });
  
          const result = await response.json();
  
          if (response.ok) {
            this.successMessage = "Profiel succesvol bijgewerkt!";
            this.errorMessage = "";
          } else {
            this.errorMessage = result.message;
            this.successMessage = "";
          }
        } catch (error) {
          console.error("Fout bij profielupdate:", error);
          this.errorMessage = "Er is een fout opgetreden. Probeer het opnieuw.";
          this.successMessage = "";
        }
      },
    },
  };
  </script>
  
  
  <style>
  .update-profile {
    max-width: 400px;
    margin: 0 auto;
    padding: 20px;
    border: 1px solid #ddd;
    border-radius: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }
  
  form div {
    margin-bottom: 15px;
  }
  
  label {
    display: block;
    margin-bottom: 5px;
  }
  
  input {
    width: 100%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 5px;
  }
  
  button {
    padding: 10px 20px;
    border: none;
    background-color: #28a745;
    color: white;
    border-radius: 5px;
    cursor: pointer;
  }
  
  button:hover {
    background-color: #218838;
  }
  
  .success {
    color: green;
  }
  
  .error {
    color: red;
  }
  </style>
  