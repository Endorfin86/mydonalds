<template>
<div id="app_wrapper">
  <Header />
  <Main />
</div>
</template>

<script>
import Header from './components/Header.vue'
import Main from './components/Main.vue'

export default {
  name: 'App',
  components: {
    Header,
    Main,
  }

}
</script>

<style>
* {
  padding: 0px;
  margin: 0px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  transition: all .4s;
}

body {
  background-color: black;
}

#app_wrapper {
  max-width: 390px;
  background-color: #ffffff;
}
</style>
