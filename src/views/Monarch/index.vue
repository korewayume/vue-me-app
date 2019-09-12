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
        value: '\n' +
          '-- 注释\n' +
          '\n' +
          '/*\n' +
          '/*\n' +
          '多行注释\n' +
          '*/\n' +
          '多行注释\n' +
          '*/\n' +
          '\n' +
          'SELECT\n' +
          '    a.sta_date as `天`,\n' +
          '    a.city as `城市`,\n' +
          '    sum(CASE WHEN retention_hour = \'0~7\' THEN 1 ELSE 0 END) as `0至7小时取件量`,\n' +
          '    sum(CASE WHEN retention_hour = \'7~24\' THEN 1 ELSE 0 END) as `7至24小时取件量`,\n' +
          '    sum(CASE WHEN retention_hour = \'7~24\' and b.post_id IS NOT NULL and b.order_type = 5 THEN 1 ELSE 0 END) as `7至24小时取件打赏量`,\n' +
          '    sum(CASE WHEN retention_hour = \'24~48\' THEN 1 ELSE 0 END) as `24至48小时的滞留件取件量`,\n' +
          '    sum(CASE WHEN retention_hour = \'24~48\' and b.post_id IS NOT NULL and b.order_type = 3 THEN 1 ELSE 0 END) as `24至48小时的滞留件取件打赏量`,\n' +
          '    sum(CASE WHEN retention_hour = \'>48\' THEN 1 ELSE 0 END) as `超过48小时的滞留件取件量`,\n' +
          '    sum(CASE WHEN retention_hour = \'>48\' and b.post_id IS NOT NULL and b.order_type = 3 THEN 1 ELSE 0 END) as `超过48小时的滞留件取件打赏量`\n' +
          'from\n' +
          '(\n' +
          '    SELECT\n' +
          '        from_unixtime(cast(pick_tm/1000 as BIGINT), \'yyyyMMdd\') AS sta_date,\n' +
          '        city,\n' +
          '        post_id,\n' +
          '        case\n' +
          '            when (cast((pick_tm-send_tm)/1000/60/60 as int)) < 7 then \'0~7\'\n' +
          '            when (cast((pick_tm-send_tm)/1000/60/60 as int)) >= 7 and (cast((pick_tm-send_tm)/1000/60/60 as int)) <= 24 then \'7~24\'\n' +
          '            when retention_tm IS NOT NULL and (cast((pick_tm-send_tm)/1000/60/60 as int)) > 24 and (cast((pick_tm-send_tm)/1000/60/60 as int)) <= 48 then \'24~48\'\n' +
          '            when retention_tm IS NOT NULL and (cast((pick_tm-send_tm)/1000/60/60 as int)) > 48 then \'>48\'\n' +
          '        END as retention_hour\n' +
          '    from dw_pdw.fact_edms_post\n' +
          '    where pt BETWEEN \'20181201\' and \'20190401\'\n' +
          '        and pick_tm >= 1543593600000\n' +
          '        and pick_tm < 1554048000000\n' +
          '        and (pick_type != 1 or busy_status != 3)\n' +
          '        and pick_tm IS NOT NULL\n' +
          '        and pick_type not in (1, 2, 4, 6, 7, 8, 14, 15)\n' +
          '\t\t\n' +
          '\tunion all\n' +
          '\t\n' +
          '\tSELECT\n' +
          '        from_unixtime(cast(pick_tm/1000 as BIGINT), \'yyyyMMdd\') AS sta_date,\n' +
          '        city,\n' +
          '        post_id,\n' +
          '        case\n' +
          '            when (cast((pick_tm-send_tm)/1000/60/60 as int)) < 7 then \'0~7\'\n' +
          '            when (cast((pick_tm-send_tm)/1000/60/60 as int)) >= 7 and (cast((pick_tm-send_tm)/1000/60/60 as int)) <= 24 then \'7~24\'\n' +
          '            when retention_tm IS NOT NULL and (cast((pick_tm-send_tm)/1000/60/60 as int)) > 24 and (cast((pick_tm-send_tm)/1000/60/60 as int)) <= 48 then \'24~48\'\n' +
          '            when retention_tm IS NOT NULL and (cast((pick_tm-send_tm)/1000/60/60 as int)) > 48 then \'>48\'\n' +
          '        END as retention_hour\n' +
          '    from dw_pdw.fact_edms_post_history\n' +
          '    where pt BETWEEN \'20181201\' and \'20190401\'\n' +
          '        and pick_tm >= 1543593600000\n' +
          '        and pick_tm < 1554048000000\n' +
          '        and (pick_type != 1 or busy_status != 3)\n' +
          '        and pick_tm IS NOT NULL\n' +
          '        and pick_type not in (1, 2, 4, 6, 7, 8, 14, 15)\n' +
          ') a\n' +
          'LEFT JOIN\n' +
          '/*dflkvlkfmev\n' +
          '*/\n' +
          '(\n' +
          '    SELECT\n' +
          '           from_unixtime(cast(pay_tm/1000 as BIGINT), \'yyyyMMdd\') AS sta_date,\n' +
          '           post_id,\n' +
          '           order_type\n' +
          '    FROM dw_pdw.fact_pick_up_pay_m\n' +
          '    WHERE pt BETWEEN \'201812\' and \'201904\'\n' +
          '          AND pay_tm >= 1543593600000\n' +
          '          AND pay_tm < 1554048000000\n' +
          '      AND order_status = 1 AND pay_status= 1\n' +
          '      and order_type in (3,5)\n' +
          ') b\n' +
          'on a.sta_date = b.sta_date and a.post_id = b.post_id\n' +
          'GROUP BY a.sta_date, a.city\n' +
          'FALSE\n' +
          'ORDER BY a.sta_date, a.city;\n' +
          'Boolean'
      })
    }
  }
</script>

<style scoped>
  #monarch {
    height: 100vh;
  }
</style>
