<template>
  <div class="container">
    <div class="button_group">
      <app-button :imgName="'back'" @click="deleteLastCharacter"></app-button>
      <app-button :imgName="'next'" @click="restoreLastCharacter"></app-button>
      <app-button :imgName="'bigText'" @click="makeTextBig"></app-button>
      <app-button :imgName="'smallText'" @click="makeTextSmall"></app-button>
      <app-button :imgName="'image'" @click="insertImage"></app-button>
      <button>Скопировать HTML</button>
    </div>
    <app-text-area v-model="text" ref="textArea"></app-text-area>
  </div>
</template>

<script>
import AppButton from './components/AppButton.vue';
import AppTextArea from './components/AppTextArea.vue';

export default {
  components: { AppButton, AppTextArea },
  data() {
    return {
      text: "",
      deletedCharacters: [],
      isPromptActive: false,
    };
  },
  methods: {
    deleteLastCharacter() {
      const editableDiv = this.$refs.textArea.$refs.editableDiv;
      const lastChild = editableDiv.lastChild;

      if (lastChild) {
        if (lastChild.nodeType === Node.TEXT_NODE) {
          const text = lastChild.textContent;
          if (text.length > 1) {
            const lastChar = text.slice(-1);
            this.deletedCharacters.push({ type: 'char', content: lastChar });
            lastChild.textContent = text.slice(0, -1);
          } else {
            this.deletedCharacters.push({ type: 'char', content: text });
            editableDiv.removeChild(lastChild);
          }
        } else {
          this.deletedCharacters.push({ type: 'node', content: lastChild });
          editableDiv.removeChild(lastChild);
        }
      }

      this.text = editableDiv.innerHTML;
    },
    restoreLastCharacter() {
      const editableDiv = this.$refs.textArea.$refs.editableDiv;
      const lastDeleted = this.deletedCharacters.pop();

      if (lastDeleted) {
        if (lastDeleted.type === 'char') {
          const textNode = editableDiv.lastChild;
          if (textNode && textNode.nodeType === Node.TEXT_NODE) {
            textNode.textContent += lastDeleted.content;
          } else {
            editableDiv.appendChild(document.createTextNode(lastDeleted.content));
          }
        } else if (lastDeleted.type === 'node') {
          editableDiv.appendChild(lastDeleted.content);
        }
      }

      this.text = editableDiv.innerHTML;
    },
    makeTextBig() {
      const editableDiv = this.$refs.textArea.$refs.editableDiv; 
      const selection = window.getSelection();
    

      const range = selection.getRangeAt(0);
      const selectedText = range.toString();

      const span = document.createElement("span");
      span.style.fontSize = "40px";
      span.textContent = selectedText;

      range.deleteContents();
      range.insertNode(span);

      selection.removeAllRanges();

      const newRange = document.createRange();
      newRange.setStartAfter(span);
      newRange.collapse(true);
      selection.addRange(newRange);

      this.text = editableDiv.innerHTML;
    },
    makeTextSmall() {
      const editableDiv = this.$refs.textArea.$refs.editableDiv;
      const selection = window.getSelection();

      const range = selection.getRangeAt(0);
      const selectedText = range.toString();


      const span = document.createElement("span");
      span.style.fontSize = "large"; 
      span.textContent = selectedText;

      range.deleteContents();
      range.insertNode(span);

      selection.removeAllRanges();

      const newRange = document.createRange();
      newRange.setStartAfter(span);
      newRange.collapse(true);
      selection.addRange(newRange);

      this.text = editableDiv.innerHTML;
    },
    insertImage() {
      if (this.isPromptActive) return; 
        this.isPromptActive = true;
      const imageUrl = prompt("Введите ссылку на изображение:");

      const editableDiv = this.$refs.textArea.$refs.editableDiv;
      const selection = window.getSelection();
      const range = selection.rangeCount ? selection.getRangeAt(0) : null;

      const img = document.createElement("img");
      img.src = imageUrl;
      img.alt = "Изображение";
      img.style.maxWidth = "100%"; 
      img.style.height = "auto";

      if (range) {
        range.deleteContents(); 
        range.insertNode(img);
      } else {
        editableDiv.appendChild(img); 
      }

      this.text = editableDiv.innerHTML;

      selection.removeAllRanges();
    },
  },
};
</script>


<style scoped>
.container {
  padding: 30px;
}

.button_group {
  display: flex;
  color: rgba(99, 158, 255, 1);
}

.button_group button {
  all: unset;
  color: rgba(99, 158, 255, 1);
  font-size: large;
  cursor: pointer;
  margin-right: 10px;
}

.button_group button:hover {
  text-decoration: underline;
}
</style>
