<template>
  <div id="app">
    <h1 class="center">Wisk Solutions</h1>
    <Dropdown class="center"></Dropdown>
    <FamiliesModal></FamiliesModal>
  </div>
</template>

<script>
import Dropdown from './components/Dropdown';
import FamiliesModal from './components/FamiliesModal';

export default {
  name: 'app',
  components: {
    Dropdown,
    FamiliesModal
  }
}
</script>

<style>
.center {
  text-align: center;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
