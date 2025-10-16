<script setup lang="ts">
import type { Event as CustomEvent } from '@/types'
import { ref, onMounted } from 'vue'
import EventService from '@/services/EventService'
import OrganizerService from '@/services/OrganizerService'
import { useRouter } from 'vue-router'
import { useMessageStore } from '@/stores/message'
import BaseInput from '@/components/BaseInput.vue'
import BaseSelect from '@/components/BaseSelect.vue'
import ImageUpload from '@/components/ImageUpload.vue'
import type { Organizer } from '@/types'

const event = ref<CustomEvent>({
  id: 0,
  category: '',
  title: '',
  description: '',
  location: '',
  date: '',
  time: '',
  petsAllowed: false,
  organizer: {
    id: 0,
    name: ''
  },
  images: []
})

const organizers = ref<Organizer[]>([])
onMounted(() => {
  OrganizerService.getOrganizers()
    .then((response) => {
      organizers.value = response.data
    })
    .catch(() => {
      router.push({ name: 'network-error-view' })
    })
})

const router = useRouter()
const store = useMessageStore()
function saveEvent() {
  const payload = JSON.parse(JSON.stringify(event.value)) // deep copy to avoid mutating the original
  if (payload.id === 0) delete payload.id
  EventService.saveEvent(payload as any)
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
      <BaseInput v-model="event.category" type="text" label="Category" />
      <h3 class="text-lg font-semibold mb-2">Name & describe your event</h3>

      <BaseInput v-model="event.title" type="text" label="Title" />

      <BaseInput v-model="event.description" type="text" label="Description" />

      <h3 class="text-lg font-semibold mb-2">Where is your event?</h3>
      <BaseInput v-model="event.location" type="text" label="Location" />

      <h3>Who is your organizer?</h3>
      <label>Select an Organizer:</label>
      <BaseSelect v-model="event.organizer.id" :options="organizers" label="Organizer"/>

      <h3 class="text-lg font-semibold mb-2">The image of the Event</h3>
      <ImageUpload v-model="event.images" />

      <button
        type="submit"
        class="button bg-gradient-to-r from-teal-500 to-green-400 text-white font-bold py-3 px-8 rounded shadow hover:scale-105 transition-transform"
      >
        Submit
      </button>
    </form>
    <pre class="mt-8 bg-gray-100 p-4 rounded">{{ event }}</pre>
  </div>
</template>
