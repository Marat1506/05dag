<template>
    <button @click="$emit('click')">
      <img :src="buttonImage" alt="Button Image" />
    </button>
  </template>
  
  <script>
  export default {
    props: {
      imgName: {
        type: String,
        required: true,
      },
    },
    computed: {
      buttonImage() {
        return new URL(`../assets/buttons/${this.imgName}.png`, import.meta.url).href;
      },
    },
  };
  </script>
  
  <style scoped>
  button {
    all: unset;
    display: inline-block;
    cursor: pointer;
  }
  
  img {
    display: block;
  }
  </style>
  