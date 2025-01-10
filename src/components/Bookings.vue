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
        try {
          const response = await fetch("http://localhost:3000/bookings");
          const data = await response.json();
          this.bookings = data;
        } catch (error) {
          console.error("Fout bij het ophalen van boekingen:", error);
        }
      },
    },
    mounted() {
      this.fetchBookings();
    },
  };
  </script>
  