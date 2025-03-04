<template>
  <div id="app">
    <!-- Navigatiebalk -->
    <nav class="navbar">
      <ul>
        <li
          v-for="(page, index) in visiblePages"
          :key="index"
          @click="changePage(page)"
          :class="{ active: activePage === page }"
        >
          {{ capitalize(page) }}
        </li>
        <li v-if="isLoggedIn" @click="handleLogout">Logout</li>
      </ul>
    </nav>

    <!-- Hoofdinhoud -->
    <div class="content">
      <Home v-if="activePage === 'home'" />
      <Register v-if="activePage === 'register'" />
      <Login v-if="activePage === 'login'" @loginSuccess="handleLogin" />
      <CampingSpots v-if="activePage === 'camping-spots'" />
      <Bookings v-if="activePage === 'bookings'" />
      <UpdateProfile v-if="activePage === 'update-profile'" :userId="userId" />
      <OwnerCampingSpots v-if="activePage === 'owner-camping-spots'" :userId="userId" />
    </div>

    <!-- Footer -->
    <footer class="footer">
      <p>&copy; 2025 Camping Booking Site. Alle rechten voorbehouden.</p>
    </footer>
  </div>
</template>

<script>
import Home from './components/Home.vue';
import Register from './components/Register.vue';
import Login from './components/Login.vue';
import CampingSpots from './components/CampingSpots.vue';
import Bookings from './components/Bookings.vue';
import UpdateProfile from './components/UpdateProfile.vue';
import OwnerCampingSpots from './components/OwnerCampingSpots.vue';

export default {
  name: 'App',
  components: {
    Home,
    Register,
    Login,
    CampingSpots,
    Bookings,
    UpdateProfile,
    OwnerCampingSpots,
  },
  data() {
    return {
      isLoggedIn: false, // Status van de gebruiker
      userId: null, // Ingelogde gebruiker-ID
      isOwner: false, // Gebruiker is eigenaar of niet
      activePage: 'home', // Standaardpagina
      pages: ['home', 'register', 'login'], // Beschikbare pagina's
    };
  },
  computed: {
    visiblePages() {
      if (!this.isLoggedIn) {
        return ['home', 'register', 'login'];
      }
      if (this.isOwner) {
        return ['home', 'update-profile', 'owner-camping-spots'];
      }
      return ['home', 'camping-spots', 'bookings', 'update-profile'];
    },
  },
  methods: {
    changePage(page) {
      this.activePage = page;
    },
    handleLogin({ userId, isOwner }) {
      this.isLoggedIn = true;
      this.isOwner = isOwner;
      this.userId = userId;

      // Verander naar de juiste pagina
      this.changePage(isOwner ? 'owner-camping-spots' : 'camping-spots');
    },
    handleLogout() {
      this.isLoggedIn = false;
      this.isOwner = false;
      this.userId = null;

      this.changePage('home'); // Reset naar home na uitloggen
    },
    checkLoginStatus() {
      // Reset status volledig bij refresh
      this.isLoggedIn = false;
      this.isOwner = false;
      this.userId = null;
      this.activePage = 'home'; // Stel standaardpagina expliciet in
    },
    capitalize(text) {
      return text.charAt(0).toUpperCase() + text.slice(1);
    },
  },
  mounted() {
    this.checkLoginStatus(); // Controleer status bij laden van de app
  },
};
</script>

<style>
/* Algemene stijlen */
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  background-color: #f9f9f9;
  color: #333;
}

#app {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

/* Navigatiebalk */
.navbar {
  background-color: #2c3e50;
  color: white;
  padding: 10px 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.navbar ul {
  list-style: none;
  display: flex;
  margin: 0;
  padding: 0;
}

.navbar ul li {
  margin: 0 15px;
  cursor: pointer;
  padding: 5px 10px;
  border-radius: 5px;
  transition: background-color 0.3s;
}

.navbar ul li:hover {
  background-color: #34495e;
}

.navbar ul li.active {
  background-color: #1abc9c;
}

/* Hoofdinhoud */
.content {
  flex: 1;
  padding: 20px;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

/* Footer */
.footer {
  background-color: #2c3e50;
  color: white;
  text-align: center;
  padding: 10px;
  margin-top: auto;
  box-shadow: 0 -2px 4px rgba(0, 0, 0, 0.1);
}

.footer p {
  margin: 0;
  font-size: 14px;
}
</style>
