<template>
    <div>
      <h1>Mijn Boekingen</h1>
      <div v-if="bookings.length === 0">Geen boekingen gevonden.</div>
      <ul v-else>
        <li v-for="booking in bookings" :key="booking.id">
          <p><strong>Camping Spot ID:</strong> {{ booking.campingSpotId }}</p>
          <p><strong>Startdatum:</strong> {{ booking.startDate }}</p>
          <p><strong>Einddatum:</strong> {{ booking.endDate }}</p>
          <p><strong>Totaalprijs:</strong> €{{ booking.totalPrice }}</p>
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  export default {
    name: "BookingsPage",
    data() {
      return {
        bookings: [],
      };
    },
    methods: {
      async fetchBookings() {
        const userId = localStorage.getItem('userId'); // Ophalen van ingelogde gebruiker-ID
  
        if (!userId) {
          console.error("Geen gebruiker-ID gevonden. Zorg ervoor dat je bent ingelogd.");
          this.bookings = [];
          return;
        }
  
        try {
          const response = await fetch(`http://localhost:3000/bookings?userId=${userId}`);
          const data = await response.json();
  
          if (response.ok) {
            this.bookings = data;
          } else {
            console.error("Fout:", data.message);
            this.bookings = [];
          }
        } catch (error) {
          console.error("Fout bij het ophalen van boekingen:", error);
          this.bookings = [];
        }
      },
    },
    mounted() {
      this.fetchBookings();
    },
  };
  </script>
  
  