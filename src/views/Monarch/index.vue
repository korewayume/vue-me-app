<template>
  <div id="monarch"></div>
</template>

<script>
  import * as monaco from 'monaco-editor'
  import './hive-language'
  const languageId = 'hive-language';

  export default {
    data() {
      return {
        editor: null
      }
    },
    mounted() {
      this.editor = monaco.editor.create(document.getElementById('monarch'), {
        scrollBeyondLastLine: false,
        theme: languageId,
        fontSize: 12,
        fontFamily: '"Ubunto Mono", Menlo, Monaco, "Courier New", monospace',
        language: languageId,
        value: ''
      })
    }
  }
</script>

<style scoped>
  #monarch {
    height: 100vh;
  }
</style>
