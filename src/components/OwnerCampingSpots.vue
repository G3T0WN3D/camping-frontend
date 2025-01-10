<template>
    <div>
      <h1>Beheer Campingplaatsen</h1>
  
      <!-- Overzicht van bestaande campings -->
      <ul>
        <li v-for="spot in campingSpots" :key="spot.id">
          <p><strong>Naam:</strong> {{ spot.name }}</p>
          <p><strong>Beschrijving:</strong> {{ spot.description }}</p>
          <p><strong>Locatie:</strong> {{ spot.location }}</p>
          <p><strong>Prijs per nacht:</strong> €{{ spot.price_per_night }}</p>
          <img :src="`http://localhost:3000/images/${spot.image_url}`" alt="Afbeelding" width="100" />
        </li>
      </ul>
  
      <!-- Formulier om een nieuwe camping toe te voegen -->
      <h2>Nieuwe Campingplaats Toevoegen</h2>
      <form @submit.prevent="addCampingSpot">
        <div>
          <label for="name">Naam:</label>
          <input type="text" id="name" v-model="newCampingSpot.name" required />
        </div>
        <div>
          <label for="description">Beschrijving:</label>
          <textarea id="description" v-model="newCampingSpot.description" required></textarea>
        </div>
        <div>
          <label for="location">Locatie:</label>
          <input type="text" id="location" v-model="newCampingSpot.location" required />
        </div>
        <div>
          <label for="price">Prijs per nacht:</label>
          <input type="number" id="price" v-model="newCampingSpot.price_per_night" required />
        </div>
        <div>
          <label for="image">Afbeelding:</label>
          <input type="file" id="image" @change="handleImageUpload" />
        </div>
        <button type="submit">Toevoegen</button>
      </form>
  
      <p v-if="successMessage" class="success">{{ successMessage }}</p>
      <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
    </div>
  </template>
  
  <script>
  export default {
    name: "OwnerCampingSpots",
    props: {
      userId: {
        type: Number,
        required: true,
      },
    },
    data() {
      return {
        campingSpots: [],
        newCampingSpot: {
          name: "",
          description: "",
          location: "",
          price_per_night: 0,
          image: null,
        },
        successMessage: "",
        errorMessage: "",
      };
    },
    methods: {
      async fetchCampingSpots() {
        try {
          const response = await fetch("http://localhost:3000/camping-spots");
          const data = await response.json();
          this.campingSpots = data;
        } catch (error) {
          console.error("Fout bij het ophalen van campingplaatsen:", error);
        }
      },
      handleImageUpload(event) {
        const file = event.target.files[0];
        if (file) {
          this.newCampingSpot.image = file;
        }
      },
      async addCampingSpot() {
        const formData = new FormData();
        formData.append("name", this.newCampingSpot.name);
        formData.append("description", this.newCampingSpot.description);
        formData.append("location", this.newCampingSpot.location);
        formData.append("price_per_night", this.newCampingSpot.price_per_night);
        formData.append("image", this.newCampingSpot.image);
        formData.append("owner_id", this.userId); // Owner_id meegeven
  
        try {
          const response = await fetch("http://localhost:3000/add-camping-spot", {
            method: "POST",
            body: formData,
          });
  
          const result = await response.json();
  
          if (response.ok) {
            this.successMessage = "Campingplaats succesvol toegevoegd!";
            this.errorMessage = "";
            this.newCampingSpot = {
              name: "",
              description: "",
              location: "",
              price_per_night: 0,
              image: null,
            };
            this.fetchCampingSpots();
          } else {
            this.successMessage = "";
            this.errorMessage = result.message;
          }
        } catch (error) {
          console.error("Fout bij het toevoegen van een campingplaats:", error);
          this.errorMessage = "Er is een fout opgetreden.";
        }
      },
    },
    mounted() {
      this.fetchCampingSpots();
    },
  };
  </script>
  
  <style>
  .success {
    color: green;
  }
  .error {
    color: red;
  }
  </style>
  