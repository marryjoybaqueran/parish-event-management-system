<script setup>
import NavBar2 from '@/components/layout/NavBar2.vue'
import ThanksGivingForm from '@/components/system/ThanksGivingForm.vue'

import { useDisplay } from 'vuetify'
const { smAndDown } = useDisplay()
</script>

<template>
  <NavBar2>
    <template #content>
      <v-container fluid>
        <div class="bg-wrapper">
          <v-img src="thanksgiving mass background image.png" class="bg-image" cover></v-img>
        </div>

        <div class="overlay-content pa-10" :class="smAndDown ? 'small-icon' : 'large-icon'">
          <v-row>
            <v-col cols="2" class="back">
              <RouterLink to="/book-event" class="router-link">
                <v-btn
                  class="rounded-circle"
                  color="indigo"
                  icon
                  v-bind:width="mobile ? '70%' : '56%'"
                >
                  <v-icon>mdi-arrow-left</v-icon>
                </v-btn>
              </RouterLink> </v-col
            ><v-col cols="10" class="title">
              <h1 :class="smAndDown ? 'small-header' : 'large-header'" class="text-center">
                <i>Thanksgiving Mass</i>
              </h1></v-col
            ></v-row
          >
        </div>

        <v-card class="mt-15" :width="smAndDown ? '100%' : '100%'">
          <ThanksGivingForm></ThanksGivingForm>
        </v-card>
      </v-container>
    </template>
  </NavBar2>
</template>

<style scoped>
.bg-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
  overflow: hidden;
}

.bg-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: blur(3px);
}

.overlay-content {
  position: absolute;
  top: 50px;
  left: 0;
  right: 0;
  z-index: 1;
  color: white;
  padding-top: 20px;
}

h1 {
  font-family: 'Segoe Script', cursive;
  text-shadow:
    -2px 0 2px white,
    0 2px 2px white,
    2px 0 2px white,
    0 -2px 2px;
  color: #000;
}

.large-header {
  font-size: 40px;
  padding-left: 250px;
}

.small-header {
  font-size: 28px;
  text-align: center;
}
</style>
