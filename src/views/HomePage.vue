<template>
  <div>
    <h1>All Repositories</h1>
    <div class="repositories-list" @scroll="handleScroll">
      <div 
        v-for="repo in repositories" 
        :key="repo.githubId" 
        class="repo-block"
      >
        <ul>
          <li><strong>githubId:</strong> {{ repo.githubId }}</li>
          <li><strong>name:</strong> {{ repo.name }}</li>
          <li><strong>stars:</strong> {{ repo.stars }}</li>
          <li>
            <strong>url:</strong>
            <a :href="repo.url" target="_blank" rel="noopener noreferrer">
              {{ repo.url }}
            </a>
          </li>
          <li><strong>description:</strong> {{ repo.description }}</li>
          <li><strong>createdAt:</strong> {{ repo.createdAt }}</li>
          <li><strong>updatedAt:</strong> {{ repo.updatedAt }}</li>
        </ul>
        <hr />
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      repositories: [],
      page: 1,
      totalPages: 1,
    };
  },
  mounted() {
    this.fetchRepositories();
  },
  methods: {
    async fetchRepositories() {
      try {
        const response = await axios.get(`http://localhost:3000/api/repositories?page=${this.page}&limit=5`);
        this.repositories = [...this.repositories, ...response.data.repositories];
        this.totalPages = response.data.totalPages;
      } catch (error) {
        console.error('Error fetching repositories:', error);
      }
    },

    handleScroll(event) {
      const container = event.target;
      if (container.scrollHeight - container.scrollTop <= container.clientHeight + 50) {
        if (this.page < this.totalPages) {
          this.page++;
          this.fetchRepositories();
        }
      }
    },
  },
};
</script>

<style scoped>
html, body {
  margin: 0;
  overflow: hidden;
  height: 100%;
}

.repositories-list {
  max-height: 75vh;
  overflow-y: auto;
  padding: 20px;
}

.repo-block {
  margin-bottom: 20px;
}

a {
  color: #007bff;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}
</style>
