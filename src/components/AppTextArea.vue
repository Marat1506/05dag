<template>
    <div
      class="canvas"
      contenteditable="true"
      @input="updateText"
      ref="editableDiv"
    ></div>
  </template>
  
  <script>
  export default {
    props: {
      modelValue: {
        type: String,
        required: true,
      },
    },
    emits: ["update:modelValue"],
    watch: {
      modelValue(newValue) {
        if (newValue !== this.$refs.editableDiv.innerHTML) {
          this.$refs.editableDiv.innerHTML = newValue;
        }
      },
    },
    mounted() {
      this.$refs.editableDiv.innerHTML = this.modelValue;
    },
    methods: {
      updateText(event) {
        this.$emit("update:modelValue", event.target.innerHTML);
      },
    },
  };
  </script>
  
  <style scoped>
  .canvas {
    background-color: rgba(30, 30, 30, 1);
    width: 100%;
    height: 100dvh;
    color: white;
    font-size: 16px;
    border: none;
    outline: none;
    padding: 10px;
    box-sizing: border-box;
    overflow-y: auto;
  }
  </style>
  