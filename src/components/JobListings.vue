<script setup>
import { reactive, defineProps, onMounted, ref } from 'vue'
import JobListing from '@/components/JobListing.vue'
import { RouterLink } from 'vue-router';
import PulseLoader from 'vue-spinner/src/PulseLoader.vue';
import axios from 'axios';
defineProps({
  limit: Number,
  showButton: {
    type: Boolean,
    default: false
  }
})

const state = reactive({
  jobs: [],
  isLoading: true
});

onMounted(async ()=> {
  try {
    const response = await axios.get('/api/jobs');
    state.jobs = response.data;
  } catch (error) {
    console.error('Error fetching jobs:', error);
  } finally {
    state.isLoading = false;
  }
});

const showAll = ref(false)
</script>

<template>
  <section class="bg-blue-50 px-4 py-10">
    <div class="container-xl lg:container m-auto">
      <h2 class="text-3xl font-bold text-green-500 mb-6 text-center">
        Browse Job
      </h2>
      <!-- Loader when is true -->
    <div v-if="state.isLoading" class="text-center text-gray-500 py-6">
      <PulseLoader/>
    </div>

<!-- Job Listings -->
      <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <JobListing
          v-for="job in showAll ? state.jobs : state.jobs.slice(0, limit || state.jobs.length)"
          :key="job.id"
          :job="job"
        />
      </div>
    </div>
  </section>

  <section v-if="showButton && !showAll" class="m-auto max-w-lg my-10 px-6">
    <RouterLink to="/jobs">
    <button
      @click="showAll = true"
      class="block w-full bg-black text-white text-center py-4 px-6 rounded-xl hover:bg-gray-700"
    >
      View All Jobs
    </button>
    </RouterLink>
  </section>
</template>
