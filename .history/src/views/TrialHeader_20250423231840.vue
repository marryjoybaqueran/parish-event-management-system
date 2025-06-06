<script setup>
import NavBar2 from '@/components/layout/NavBar.vue'
import { ref, onMounted } from 'vue'
import { supabase } from '@/utils/supabase.js'

const STORAGE_KEY = 'events'
const events = ref([])
const dialog = ref(false)

const newEvent = ref({
  title: '',
  datePosted: '',
  image: '',
  file: null,
  summary: '',
  details: '',
  type: '',
  priest: '',
  additionalInfo: [],
})

async function loadEvents() {
  // Fetch events from Supabase when the page loads
  const { data, error } = await supabase.from('events').select()
  if (data) {
    events.value = data
  } else {
    console.error(error)
  }
}

async function saveEventToSupabase(event) {
  // Save event to Supabase without the image (no image URL)
  const { error } = await supabase.from('events').insert([
    {
      title: event.title,
      date_posted: event.datePosted,
      summary: event.summary,
      details: event.details,
      type: event.type,
      priest: event.priest,
      additional_info: event.additionalInfo, // Storing as JSON
    },
  ])
  if (error) {
    console.error('Error saving event to Supabase:', error)
  }
}

function handleFileUpload(event) {
  const file = event.target.files[0]
  if (file) {
    const reader = new FileReader()
    reader.onload = (e) => {
      newEvent.value.image = e.target.result // Save image locally
    }
    reader.readAsDataURL(file)
  }
}

function handleEditFileUpload(e, eventItem) {
  const file = e.target.files[0]
  if (file) {
    const reader = new FileReader()
    reader.onload = (ev) => {
      eventItem.image = ev.target.result // Save image locally
      saveEvents()
    }
    reader.readAsDataURL(file)
  }
}

async function addEvent() {
  if (newEvent.value.title) {
    // Save event to Supabase
    await saveEventToSupabase(newEvent.value)

    // Push the new event to the events array
    events.value.push({
      id: Date.now(), // Fake ID until we get the Supabase-generated ID
      ...newEvent.value,
      isEditing: false,
    })

    dialog.value = false
    newEvent.value = {
      title: '',
      datePosted: '',
      image: '',
      file: null,
      summary: '',
      details: '',
      type: '',
      priest: '',
      additionalInfo: [],
    }
  }
}

function deleteEvent(id) {
  events.value = events.value.filter((e) => e.id !== id)
  saveEvents()
}

function addAdditionalInfo(event) {
  event.additionalInfo.push({ text: '' })
}

function removeAdditionalInfo(event, index) {
  event.additionalInfo.splice(index, 1)
  saveEvents()
}

function toggleEdit(event) {
  if (event.isEditing) {
    saveEvents()
  }
  event.isEditing = !event.isEditing
}

onMounted(loadEvents)
</script>

<template>
  <NavBar2>
    <template #content>
      <v-container fluid>
        <v-row>
          <v-col><h3 class="uppercase-text">📋 Admin — Manage Events</h3></v-col>
          <v-col class="text-right">
            <v-btn color="green" @click="dialog = true">➕ Add New Event</v-btn>
          </v-col>
        </v-row>

        <v-row>
          <v-col v-for="event in events" :key="event.id" cols="12" md="4">
            <v-card class="mx-auto card-shadow" max-width="344">
              <v-img :src="event.image" height="200px" cover></v-img>
              <v-card-title>{{ event.title }}</v-card-title>
              <v-card-subtitle><b>Date Posted:</b> {{ event.datePosted }}</v-card-subtitle>
              <v-card-text>{{ event.summary }}</v-card-text>

              <v-card-actions>
                <v-btn @click="event.showDetails = !event.showDetails">
                  {{ event.showDetails ? 'Hide' : 'View Details' }}
                </v-btn>
                <v-btn color="primary" @click="toggleEdit(event)">
                  {{ event.isEditing ? 'Done' : 'Edit' }}
                </v-btn>
                <v-btn color="red" @click="deleteEvent(event.id)">🗑️ Delete</v-btn>
              </v-card-actions>

              <v-expand-transition>
                <div v-show="event.showDetails" class="pa-4">
                  <template v-if="event.isEditing">
                    <v-text-field v-model="event.title" label="Event Title" dense outlined />
                    <v-text-field v-model="event.datePosted" label="Date Posted" dense outlined />
                    <v-file-input
                      accept="image/*"
                      @change="(e) => handleEditFileUpload(e, event)"
                      label="Image File"
                      dense
                      outlined
                    />
                    <v-text-field v-model="event.type" label="Type of Event" dense outlined />
                    <v-text-field v-model="event.priest" label="Name of Priest" dense outlined />
                    <v-textarea v-model="event.summary" label="Summary" dense outlined />
                    <v-textarea v-model="event.details" label="Full Details" dense outlined />

                    <div
                      v-for="(info, index) in event.additionalInfo"
                      :key="index"
                      class="d-flex align-center mb-2"
                    >
                      <v-text-field
                        v-model="info.text"
                        label="Additional Info"
                        dense
                        outlined
                        class="flex-grow-1"
                      />
                      <v-btn icon @click="removeAdditionalInfo(event, index)"
                        ><v-icon>mdi-close</v-icon></v-btn
                      >
                    </div>

                    <v-btn color="primary" text small @click="addAdditionalInfo(event)">
                      <v-icon left>mdi-plus</v-icon> Add Info
                    </v-btn>
                  </template>

                  <template v-else>
                    <p><b>Type:</b> {{ event.type }}</p>
                    <p><b>Priest:</b> {{ event.priest }}</p>
                    <p><b>Details:</b> {{ event.details }}</p>
                    <div v-if="event.additionalInfo.length">
                      <p><b>Additional Info:</b></p>
                      <ul>
                        <li v-for="(info, i) in event.additionalInfo" :key="i">{{ info.text }}</li>
                      </ul>
                    </div>
                  </template>
                </div>
              </v-expand-transition>
            </v-card>
          </v-col>
        </v-row>

        <!-- ADD NEW EVENT DIALOG -->
        <v-dialog v-model="dialog" max-width="600">
          <v-card>
            <v-card-title>Add New Event</v-card-title>
            <v-card-text>
              <v-text-field v-model="newEvent.title" label="Title" />
              <v-text-field v-model="newEvent.datePosted" label="Date Posted" />
              <v-file-input
                accept="image/*"
                @change="handleFileUpload"
                label="Upload Image (jpg/png)"
              />
              <v-text-field v-model="newEvent.type" label="Type of Event" />
              <v-text-field v-model="newEvent.priest" label="Name of Priest" />
              <v-textarea v-model="newEvent.summary" label="Summary" />
              <v-textarea v-model="newEvent.details" label="Full Details" />

              <div
                v-for="(info, index) in newEvent.additionalInfo"
                :key="index"
                class="d-flex align-center mb-2"
              >
                <v-text-field
                  v-model="info.text"
                  label="Additional Info"
                  dense
                  outlined
                  class="flex-grow-1"
                />
                <v-btn icon @click="newEvent.additionalInfo.splice(index, 1)"
                  ><v-icon>mdi-close</v-icon></v-btn
                >
              </div>
              <v-btn color="primary" text small @click="newEvent.additionalInfo.push({ text: '' })">
                <v-icon left>mdi-plus</v-icon> Add Info
              </v-btn>
            </v-card-text>
            <v-card-actions>
              <v-btn color="green" @click="addEvent"> Save Event</v-btn>
              <v-btn text @click="dialog = false">Cancel</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-container>
    </template>
  </NavBar2>
</template>

<style scoped>
.card-shadow {
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}
.flex-grow-1 {
  flex-grow: 1;
}
</style>
