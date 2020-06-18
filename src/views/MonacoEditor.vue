<template>
  <div class="monaco-wrapper">
    <div id="monaco"></div>
  </div>
</template>

<script>
  import * as monaco from 'monaco-editor'
  import './hive-language'

  export default {
    name: 'home',
    data() {
      return {
        editor: null
      }
    },
    mounted() {
      this.editor = monaco.editor.create(document.getElementById('monaco'), {
        fontSize: 16,
        fontFamily: '"Ubunto Mono", Menlo, Monaco, "Courier New", monospace',
        language: 'hive'
      })
    }
  }
</script>
<style scoped>
  .monaco-wrapper {
    height: 100vh;
  }

  #monaco {
    height: 100%;
  }
</style>
