<template>
    <div>
      <p>Це дочірній компонент</p>
      <p>{{ message }}</p>
      <button @click="changeMessage">Кнопка</button>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      message: String
    },
    methods: {
        changeMessage(){
            this.$emit('childEvent', 'Добре');
        }
    },
  };
  </script>
  