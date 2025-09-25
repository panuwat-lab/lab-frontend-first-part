<script setup lang="ts">
import type { Event as CustomEvent } from '@/types' 
import { ref } from 'vue'
import EventService from '@/services/EventService'
import { useRouter } from 'vue-router'
import { useMessageStore } from '@/stores/message'

const event = ref<CustomEvent>({
  category: '',
  title: '',
  description: '',
  location: '',
  date: '',
  time: '',
  petsAllowed: false,
  organizer: ''
})

const router = useRouter()
const store = useMessageStore()
function saveEvent() {
  // Create a copy without id
  const { id, ...eventData } = event.value
  EventService.saveEvent(eventData)
    .then((response) => {
      router.push({ name: 'event-detail-view', params: { id: response.data.id } })
      store.updateMessage('You have successfully created an a new event for ' + response.data.title)
      setTimeout(() => {
        store.updateMessage('')
      }, 3000)
    })
    .catch(() => {
      router.push({ name: 'network-error-view' })
    })
}
</script>

<template>
  <div class="max-w-xl mx-auto p-8 bg-white rounded-lg shadow">
    <h1 class="text-2xl font-bold mb-6">Create an event</h1>
    <form @submit.prevent="saveEvent">
      <label class="block text-gray-700 font-semibold mb-2">Category</label>
      <input
        v-model="event.category"
        type="text"
        placeholder="Category"
        class="field mb-6 border border-gray-400 rounded px-3 py-2 w-full focus:border-green-500 focus:outline-none"
      />

      <h3 class="text-lg font-semibold mb-2">Name & describe your event</h3>
      <label class="block text-gray-700 font-semibold mb-2">Title</label>
      <input
        v-model="event.title"
        type="text"
        placeholder="Title"
        class="field mb-6 border border-gray-400 rounded px-3 py-2 w-full focus:border-green-500 focus:outline-none"
      />

      <label class="block text-gray-700 font-semibold mb-2">Description</label>
      <input
        v-model="event.description"
        type="text"
        placeholder="Description"
        class="field mb-6 border border-gray-400 rounded px-3 py-2 w-full focus:border-green-500 focus:outline-none"
      />

      <h3 class="text-lg font-semibold mb-2">Where is your event?</h3>
      <label class="block text-gray-700 font-semibold mb-2">Location</label>
      <input
        v-model="event.location"
        type="text"
        placeholder="Location"
        class="field mb-6 border border-gray-400 rounded px-3 py-2 w-full focus:border-green-500 focus:outline-none"
      />

      <button type="submit" class="button bg-gradient-to-r from-teal-500 to-green-400 text-white font-bold py-3 px-8 rounded shadow hover:scale-105 transition-transform">
        Submit
      </button>
    </form>
    <pre class="mt-8 bg-gray-100 p-4 rounded">{{ event }}</pre>
  </div>
</template>