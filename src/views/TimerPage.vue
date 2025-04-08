<template>
  <div>
    <h1>Sync Timer</h1>
    <div>
      <p>Last Sync Time: {{ formattedLastSyncTime }}</p>
      <p>Next Sync Time: {{ formattedNextSyncTime }}</p>
    </div>
    <button @click="syncNow" class="sync-button">Sync Now</button>
    <div class="sync-interval-form">
      <input 
        type="number" 
        v-model="newIntervalInMinutes" 
        placeholder="Enter sync interval in minutes"
        min="1" 
        step="1" 
      />
      <span>minutes</span>
    </div>
    <button @click="setSyncInterval" class="set-sync-button">Set Sync Interval</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      lastSyncTime: null,
      nextSyncTime: null,
      newIntervalInMinutes: 60,
      syncIntervalId: null,
    };
  },
  mounted() {
    this.fetchSyncTimes();
    this.startSyncTimer();
  },
  beforeUnmount() {
    if (this.syncIntervalId) {
      clearInterval(this.syncIntervalId);
    }
  },
  computed: {
    formattedLastSyncTime() {
      return this.lastSyncTime
        ? this.lastSyncTime.toLocaleString([], { hour12: false })
        : '';
    },
    formattedNextSyncTime() {
      return this.nextSyncTime
        ? this.nextSyncTime.toLocaleString([], { hour12: false })
        : '';
    }
  },
  methods: {
    async fetchSyncTimes() {
      try {
        const response = await axios.post('http://localhost:3000/api/sync');
        this.lastSyncTime = new Date();
        this.nextSyncTime = new Date(response.data.nextSyncTime);
      } catch (error) {
        console.error('Error fetching sync times:', error);
      }
    },
    async syncNow() {
      try {
        const response = await axios.post('http://localhost:3000/api/sync');
        this.lastSyncTime = new Date();
        this.nextSyncTime = new Date(response.data.nextSyncTime);
      } catch (error) {
        console.error('Error syncing now:', error);
      }
    },
    async setSyncInterval() {
      const intervalInMilliseconds = this.newIntervalInMinutes * 60000;

      try {
        const response = await axios.post('http://localhost:3000/api/setSyncInterval', {
          interval: intervalInMilliseconds,
        });
        this.nextSyncTime = new Date(response.data.nextSyncTime);
        alert('Sync interval updated successfully!');
      } catch (error) {
        console.error('Error setting sync interval:', error);
      }
    },
    startSyncTimer() {
      this.syncIntervalId = setInterval(async () => {
        await this.fetchSyncTimes();
      }, 60000);
    },
  },
};
</script>

<style scoped>
.sync-button {
  margin-bottom: 8px;
}

.sync-interval-form {
  margin-top: 8px;
  margin-bottom: 8px;
}

</style>
