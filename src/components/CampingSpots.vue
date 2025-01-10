<template>
    <div>
      <h1>Camping Spots</h1>
  
      <!-- Zoek- en sorteerfunctionaliteit -->
      <div class="filter-bar">
        <input
          v-model="searchQuery"
          type="text"
          placeholder="Zoek op locatie..."
          class="search-bar"
        />
  
        <select v-model="sortOrder" class="sort-dropdown">
          <option value="none">Sorteer op...</option>
          <option value="asc">Prijs: laag naar hoog</option>
          <option value="desc">Prijs: hoog naar laag</option>
        </select>
      </div>
  
      <!-- Lijst met campingplaatsen -->
      <div v-if="filteredSpots.length" class="camping-spot-list">
        <div v-for="spot in filteredSpots" :key="spot.id" class="camping-spot-card">
          <img
            :src="`http://localhost:3000/${spot.image_url}`"
            :alt="spot.name"
            class="camping-spot-image"
          />
          <div class="camping-spot-details">
            <h2>{{ spot.name }}</h2>
            <p>{{ spot.description }}</p>
            <p><strong>Locatie:</strong> {{ spot.location }}</p>
            <p><strong>Prijs per nacht:</strong> €{{ spot.price_per_night }}</p>
  
            <!-- Datumkiezer -->
            <div>
              <label>Startdatum:</label>
              <input
                type="date"
                :value="selectedDates[spot.id]?.startDate || ''"
                @change="updateDate(spot.id, 'startDate', $event.target.value)"
                :min="minDate"
              />
  
              <label>Einddatum:</label>
              <input
                type="date"
                :value="selectedDates[spot.id]?.endDate || ''"
                @change="updateDate(spot.id, 'endDate', $event.target.value)"
                :min="selectedDates[spot.id]?.startDate || minDate"
              />
            </div>
  
            <!-- Boekingsknop -->
            <button @click="bookSpot(spot.id)" :disabled="!isDateValid(spot.id)">
              Boeken
            </button>
          </div>
        </div>
      </div>
  
      <!-- Geen resultaten -->
      <p v-else class="no-results">Geen resultaten gevonden voor "{{ searchQuery }}"</p>
    </div>
  </template>
  
  <script>
  export default {
    name: "CampingSpots",
    data() {
      return {
        campingSpots: [],
        selectedDates: {}, // Opslag voor geselecteerde data per camping spot
        minDate: new Date().toISOString().split("T")[0], // Vandaag als minimale datum
        searchQuery: "", // Zoekterm
        sortOrder: "none", // Sorteervolgorde
      };
    },
    computed: {
      filteredSpots() {
        // Filteren op locatie
        let spots = this.campingSpots.filter((spot) =>
          spot.location.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
  
        // Sorteren op prijs
        if (this.sortOrder === "asc") {
          spots.sort((a, b) => a.price_per_night - b.price_per_night);
        } else if (this.sortOrder === "desc") {
          spots.sort((a, b) => b.price_per_night - a.price_per_night);
        }
  
        return spots;
      },
    },
    methods: {
      async fetchCampingSpots() {
        try {
          const response = await fetch("http://localhost:3000/camping-spots");
          const data = await response.json();
          this.campingSpots = data;
  
          // Initializeer geselecteerde datums voor elke camping spot
          this.campingSpots.forEach((spot) => {
            if (!this.selectedDates[spot.id]) {
              this.selectedDates[spot.id] = { startDate: "", endDate: "" };
            }
          });
        } catch (error) {
          console.error("Fout bij het ophalen van camping spots:", error);
        }
      },
      updateDate(spotId, key, value) {
        if (!this.selectedDates[spotId]) {
          this.selectedDates[spotId] = { startDate: "", endDate: "" };
        }
        this.selectedDates[spotId][key] = value;
      },
      isDateValid(spotId) {
        const dates = this.selectedDates[spotId];
        return (
          dates &&
          dates.startDate &&
          dates.endDate &&
          new Date(dates.startDate) < new Date(dates.endDate)
        );
      },
      async bookSpot(spotId) {
        const { startDate, endDate } = this.selectedDates[spotId];
  
        if (!this.isDateValid(spotId)) {
          alert("Selecteer een geldige datum!");
          return;
        }
  
        // Bereken totalPrice
        const campingSpot = this.campingSpots.find((spot) => spot.id === spotId);
        const pricePerNight = campingSpot.price_per_night;
        const days =
          (new Date(endDate).getTime() - new Date(startDate).getTime()) /
          (1000 * 60 * 60 * 24); // verschil in dagen
        const totalPrice = days * pricePerNight;
  
        try {
          console.log("Boeking verstuurd:", {
            campingSpotId: spotId,
            startDate,
            endDate,
            totalPrice,
            userId: 1, // Vervang met de daadwerkelijke ingelogde gebruiker-ID
          });
  
          const response = await fetch("http://localhost:3000/book", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({
              campingSpotId: spotId,
              startDate,
              endDate,
              totalPrice,
              userId: 1, // Placeholder voor gebruiker-ID
            }),
          });
  
          if (response.ok) {
            alert("Boeking succesvol!");
          } else {
            const data = await response.json();
            alert(`Fout: ${data.message}`);
          }
        } catch (error) {
          console.error("Fout bij het boeken:", error);
          alert("Er is een probleem opgetreden bij het boeken.");
        }
      },
    },
    mounted() {
      this.fetchCampingSpots();
    },
  };
  </script>
  
  <style>
  .filter-bar {
    display: flex;
    justify-content: space-between;
    margin-bottom: 20px;
  }
  
  .search-bar {
    padding: 10px;
    font-size: 16px;
    width: 70%;
  }
  
  .sort-dropdown {
    padding: 10px;
    font-size: 16px;
    width: 25%;
  }
  
  .no-results {
    text-align: center;
    font-size: 18px;
    color: gray;
  }
  
  .camping-spot-list {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
  }
  
  .camping-spot-card {
    border: 1px solid #ddd;
    border-radius: 10px;
    padding: 15px;
    width: calc(33% - 20px);
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }
  
  .camping-spot-image {
    width: 100%;
    border-radius: 10px;
  }
  
  .camping-spot-details h2 {
    margin: 10px 0;
  }
  </style>
  