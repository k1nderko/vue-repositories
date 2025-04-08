<template>
  <div id="app">
    <nav>
      <router-link to="/">HomePage</router-link>
      <router-link to="/search">SearchPage</router-link>
      <router-link to="/timer">TimerPage</router-link>
    </nav>
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App',
};
</script>

<style>
#app {
  overflow: hidden;
}

nav {
  margin-bottom: 20px;
}

nav a {
  margin-right: 10px;
}
</style>

