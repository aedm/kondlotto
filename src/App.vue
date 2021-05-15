<template>
    <KondLotto/>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import KondLotto from "./components/KondLotto.vue";

export default defineComponent({
  name: 'App',
  components: {
    KondLotto,
  }
})
</script>
