<script setup>
import { ref, computed } from 'vue'
const theme = ref(localStorage.getItem('theme') ?? 'light')

const isDark = computed({
  get: () => theme.value === 'dark',
  set: (val) => {
    theme.value = val ? 'dark' : 'light'
    localStorage.setItem('theme', theme.value)
  },
})

const drawer = ref(false)

function onClick() {
  localStorage.setItem('theme', theme.value)
}
import { useDisplay } from 'vuetify'
const { mobile } = useDisplay()
const { smAndDown } = useDisplay()
</script>

<template>
  <v-responsive class="border rounded">
    <v-app :theme="theme">
      <v-app-bar
        class="px-6"
        :color="theme === 'light' ? 'light-blue-lighten-1' : 'light-blue-accent-4'"
        border
      >
        <!-- LOGO + HEADER -->
        <div class="d-flex align-center">
          <v-img src="logo.png" :width="mobile ? '40px' : '45px'" class="me-2" cover />
          <h2 :class="smAndDown ? 'small-header' : 'large-header'" class="ma-0 header">
            SAN ISIDRO LABRADOR PARISH
          </h2>
        </div>

        <v-spacer></v-spacer>

        <!-- Desktop Nav -->
        <div v-if="!smAndDown" class="d-flex align-center nav">
          <div class="d-flex nav">
            <v-btn class="home-btn mr-2" flat>
              <v-icon class="home-icon">mdi-home</v-icon>
              <span class="home-text"> <span class="home-color">HOME</span></span>
            </v-btn>

            <RouterLink to="/book-event" class="router-link">
              <v-btn class="mr-2 outlined-btn" outlined>
                <v-icon class="event-icon">mdi-calendar</v-icon>
                <span
                  class="hover-underline-animation"
                  :class="smAndDown ? 'small-header' : 'large-header'"
                  >BOOK EVENT</span
                >
              </v-btn>
            </RouterLink>
          </div>
          <v-spacer></v-spacer>

          <v-switch
            v-model="isDark"
            color="primary"
            hide-details
            class="theme-switch pl-6"
            @change="onClick"
            style="transform: scale(1.5); transform-origin: right center"
          >
            <template #thumb>
              <v-icon size="20">
                {{ isDark ? 'mdi-weather-night' : 'mdi-weather-sunny' }}
              </v-icon>
            </template>
          </v-switch>
        </div>

        <!-- Mobile Nav (Hamburger) -->
        <div v-else>
          <v-btn icon @click="drawer = !drawer">
            <v-icon>mdi-menu</v-icon>
          </v-btn>
        </div>
      </v-app-bar>

      <!-- MOBILE DRAWER MENU -->
      <v-navigation-drawer v-model="drawer" temporary location="right">
        <v-list>
          <!-- HOME -->
          <v-list-item @click="drawer = false">
            <RouterLink to="/" class="router-link" style="width: 100%">
              <v-btn flat>
                <v-icon class="me-2">mdi-home</v-icon>
                HOME
              </v-btn>
            </RouterLink>
          </v-list-item>

          <!-- BOOK EVENT -->
          <v-list-item @click="drawer = false">
            <RouterLink to="/book-event" class="router-link" style="width: 100%">
              <v-btn flat>
                <v-icon class="me-2">mdi-calendar</v-icon>
                BOOK EVENT
              </v-btn>
            </RouterLink>
          </v-list-item>

          <!-- THEME SWITCH -->
          <v-list-item>
            <v-btn
              v-model="isDark"
              @click="isDark = !isDark"
              hide-details
              class="theme-switch"
              @change="onClick"
              flat
            >
              <span>
                <v-icon size="20" class="me-2">
                  {{ isDark ? 'mdi-weather-night' : 'mdi-weather-sunny' }} </v-icon
                >{{ isDark ? 'Light Mode' : 'Dark Mode' }}
              </span>
            </v-btn>
          </v-list-item>
        </v-list>
      </v-navigation-drawer>
      <!-- MAIN CONTENT -->
      <v-main>
        <v-container>
          <slot name="content"></slot>
        </v-container>
      </v-main>
    </v-app>
  </v-responsive>
</template>

<style scoped>
.header {
  font-family: 'Jomolhari', serif;
  font-weight: 500;
  color: black;
}

.nav {
  .poppins-bold {
    font-family: 'Poppins', sans-serif;
    font-weight: 700;
    font-style: normal;
  }
}

.hover-underline-animation {
  position: relative;
  display: inline-flex;
  align-items: center;
  padding-bottom: 3px;
  gap: 8px;
  color: black;
  font-weight: 600;
}

.hover-underline-animation::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 1px;
  background-color: white;
  transform: scaleX(0);
  transform-origin: center;
  transition: transform 0.3s ease-out;
}

.hover-underline-animation:hover::after {
  transform: scaleX(1);
}

.outlined-btn {
  padding: 4px 12px;
  gap: 8px;
}

.home-btn {
  position: relative;
  border-radius: 12px;
  border: none;
  color: black;
  cursor: default;
  background-color: transparent !important;
  box-shadow: none !important;
  display: flex;
  align-items: center;
  gap: 20px;
  pointer-events: none;
  padding-bottom: 1px;
  letter-spacing: 2px;
  font-weight: 600;
}

.home-btn:hover {
  background-color: transparent !important;
  color: black !important;
  box-shadow: none !important;
}

.home-icon {
  font-size: 22px;
  margin-right: 5px;
}

.event-icon {
  font-size: 22px;
  vertical-align: middle;
  margin-right: 5px;
  color: black;
}

.home-color {
  border-bottom: 1px solid white;
  padding-bottom: 1px;
}

/*added */
.home-color::after {
  content: '';
  position: absolute;
  width: 100%;
  transform: scaleX(0);
  height: 2px;
  bottom: 0;
  left: 0;
  background-color: #0087ca;
  transition: transform 0.25s ease-out;
}

.router-link {
  text-decoration: none;
  color: inherit;
}

.router-link:hover {
  text-decoration: none;
}

.large-header {
  font-size: 15px;
  font-weight: 600;
  letter-spacing: 1px;
}

.small-header {
  font-size: 10px;
  font-weight: 500;
  letter-spacing: 0.5px;
}
</style>
