<template>
  <div class="mx-auto max-w-md" @click="onClick">
    <div class="bg-white h-16 sm:h-20 mx-4 mt-6 shadow-md rounded-md sm:rounded-xl overflow-hidden flex">
      <div class="bg-gray-900 px-6 sm:px-8 flex items-center">
        <p class="text-lg text-white font-bold">USD / HUF</p>
      </div>
      <div class="bg-white pl-6 pr-4 flex items-center">
        <div>
          <p class="text-xl font-bold">290.1</p>
          <p class="text-xs sm:text-sm">{{count}} minutes ago</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">

import { useStore } from "../store/store";
import { computed } from "vue";

export default {
  setup () {
    const store = useStore();
    return {
      count: computed(() => store.state.count),
      onClick: () => store.commit('increment'),
    }
  }
}

</script>