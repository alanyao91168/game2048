<template>
  <q-page class="flex flex-center">
    <div class="text-center">
      <h1 class="text-h2 q-mb-md">热门游戏 2048</h1>
      <p class="text-subtitle1 q-mb-lg">来挑战你的数学技能吧!</p>
      <q-btn color="primary" label="开始游戏" @click="startGame" />
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'PageIndex',
  methods: {
    startGame() {
      this.$router.push('/game');
    },
  },
});
</script>
