<template>
  <div id="app">
    <router-view/>
    <Footer />
  </div>
</template>

<script>
import Footer from "@/components/Footer";

export default {
  name: "App",
  components: {
    Footer
  }
};
</script>
<style>
body {
  background: #1a1c79;
  height: 100%;
  position: fixed;
  width: 100%;
}
</style>

<style scoped>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
}
#nav a {
  font-weight: bold;
  color: #2c3e50;
}
#nav a.router-link-exact-active {
  color: #42b983;
}
.header-image {
  height: 70px;
  cursor: pointer;
}
</style>
