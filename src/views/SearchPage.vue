<template>
  <div>
    <h1>Search Repository</h1>
    <form @submit.prevent="searchRepository">
      <input
        v-model="query"
        type="text"
        placeholder="Enter repository name or GitHub ID"
      />
      <button type="submit">Search</button>
    </form>

    <div v-if="searchResult">
      <h2>Repository Details</h2>
      <ul>
        <li><strong>githubId:</strong> {{ searchResult.githubId }}</li>
        <li><strong>name:</strong> {{ searchResult.name }}</li>
        <li><strong>stars:</strong> {{ searchResult.stars }}</li>
        <li>
          <strong>url:</strong>
          <a :href="searchResult.url" target="_blank" rel="noopener noreferrer">
            {{ searchResult.url }}
          </a>
        </li>
        <li><strong>description:</strong> {{ searchResult.description }}</li>
        <li><strong>createdAt:</strong> {{ searchResult.createdAt }}</li>
        <li><strong>updatedAt:</strong> {{ searchResult.updatedAt }}</li>
      </ul>
    </div>

    <div v-else-if="errorMessage">
      <p style="color: red;">{{ errorMessage }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'SearchPage',
  data() {
    return {
      query: '',
      searchResult: null,
      errorMessage: null,
    };
  },
  computed: {
    formattedResult() {
      return JSON.stringify(this.searchResult, null, 2);
    },
  },
  methods: {
    async searchRepository() {
      this.errorMessage = null;
      this.searchResult = null;

      const isNumber = /^\d+$/.test(this.query);

      try {
        const response = await axios.get('http://localhost:3000/api/repository', {
          params: isNumber ? { githubId: this.query } : { name: this.query },
        });

        this.searchResult = response.data;
      } catch (error) {
        if (error.response) {
          this.errorMessage = error.response.data.message || 'Error searching repository';
        } else if (error.request) {
          this.errorMessage = 'No response from server';
        } else {
          this.errorMessage = 'Error setting up request';
        }
        console.error(error);
      }
    },
  },
};
</script>

<style scoped>
input {
  padding: 0.5rem;
  margin-right: 0.5rem;
  width: 100%;
  max-width: 600px;
  box-sizing: border-box;
}

button {
  padding: 0.5rem 1rem;
}

form {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 1rem;
  width: 100%;
  max-width: 650px;
}

pre {
  background-color: #f4f4f4;
  padding: 1rem;
  border-radius: 8px;
  white-space: pre-wrap;
  word-break: break-word;
}
</style>
