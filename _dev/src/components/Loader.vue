<script setup>
import { defineProps, defineComponent } from 'vue'
import Icon from './Icon.vue'
import Button from './Button.vue'

defineComponent({
  Button,
  Icon
})

defineProps({
  visible: {
    type: Boolean,
    default: false
  }
})
</script>

<template>
  <div class="flex gap-2 justify-center mt-2" v-if="visible">
    <Button type="primary">
      <Icon name="RefreshIcon" class="duration-700 animate-spin h-5 w-5" />
      <slot></slot>
    </Button>
  </div>
</template>
