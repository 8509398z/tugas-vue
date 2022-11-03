<template>
    <div class="header">
      <h1>
        {{ title }}
      </h1>
    </div>
  </template>
  
  
  <script>
  export default {
    data() {
      return {
        title: "Tugas Kelas Vue.js @ 2022"
      }
    }
  }
  </script>
  
  <style scoped>
  div.header {
    background-color: blue;
    color: white;
    padding: 10px;
    height: 40px;
  }
  h1 {
    font-size: 28px;
    margin: 0px;
}
  </style>