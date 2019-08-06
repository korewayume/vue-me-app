<template>
  <div class="monaco-wrapper">
    <div id="monaco"></div>
  </div>
</template>

<script>
  import * as monaco from 'monaco-editor'
  import './hive-language'

  const sql = "WITH ED_COUNT AS\n" +
    "  (SELECT cast(pt AS BIGINT) pt,\n" +
    "          count(1) ed_count\n" +
    "   FROM dw_pdw.dim_ed_info_d\n" +
    "   GROUP BY pt)\n" +
    "SELECT a.pt,\n" +
    "       max(a.ed_count) ed_count,\n" +
    "       sum(b.ed_count) sum_ed_count\n" +
    "FROM ED_COUNT a\n" +
    "CROSS JOIN ED_COUNT b\n" +
    "WHERE a.pt >= b.pt\n" +
    "GROUP BY a.pt\n" +
    "ORDER BY a.pt";

  export default {
    name: 'home',
    data() {
      return {
        editor: null
      }
    },
    mounted() {
      this.editor = monaco.editor.create(document.getElementById('monaco'), {
        value: sql,
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
