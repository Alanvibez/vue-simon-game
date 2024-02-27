<template>
  <li
    :class="[props.color, { flash: props.isFlashing || isActive }]"
    @click="handleClick"
  ></li>
</template>

<script setup>
import { ref } from 'vue'

const isActive = ref(false)

const props = defineProps(['color', 'isFlashing'])
const emit = defineEmits(['click'])

const handleClick = () => {
  emit('click', props.color)
  isActive.value = !isActive.value
  setTimeout(() => (isActive.value = !isActive.value), 200)
}
</script>

<style scoped>
li {
  background: white;
  width: 100%;
  height: 100%;
  list-style: none;
}

li {
  opacity: 0.5;
  transition: all 0.5s linear;
  cursor: pointer;
}
li.red {
  background: rgb(255, 8, 8);
}

li.yellow {
  background: rgb(254, 254, 9);
}

li.green {
  background: #18ff08;
}

li.blue {
  background: rgb(12, 132, 252);
}
li.flash {
  opacity: 1;
  filter: drop-shadow(2px 4px 6px black);
}
</style>
