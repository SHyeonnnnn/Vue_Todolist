<template>
  <nav>
    <ul>
      <li><router-link to="/">HOME</router-link></li>
      <li><router-link to="/todos">TODOS</router-link></li>
    </ul>
  </nav>
  <router-view/>
</template>

<script>
  export default {

  }
</script>

<style>
  *{text-decoration: none; color: #000;}
</style>
