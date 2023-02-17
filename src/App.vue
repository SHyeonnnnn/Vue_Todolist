<template>
  <div>
    <nav class="navbar">
      <ul>
        <!-- <li><router-link to="/">HOME</router-link></li>
        <li><router-link to="/todos">TODOS</router-link></li> -->
        <li><router-link :to="{ name:'Home' }">HOME</router-link></li>
        <li><router-link :to="{ name:'Todos' }">TODOS</router-link></li>
      </ul>
    </nav>
    <router-view/>
  </div>
</template>

<script>
  export default {

  }
</script>

<style>
  *{text-decoration: none; color: #000;}
  .navbar{border: 1px solid #ddd;width: 100%;padding: 20px;}
  .navbar ul{max-width: 1024px;width: 100%;list-style: none;display: flex;margin: 0 auto;}
  .navbar ul li a{padding: 10px 20px;font-weight: bold;font-size: 18px;color: #27c291;text-shadow: 2px 1px 2px #36767e;}
  .navbar ul li a:hover{color: #07fbae;text-shadow: 2px 1px 2px #36767e;}
  .navbar ul li a:active{color: #19664e;text-shadow: 2px 1px 2px #36767e;}
</style>
