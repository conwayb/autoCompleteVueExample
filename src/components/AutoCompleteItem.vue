<template>
  <div>
    <span :class="item.text.toLowerCase()">
      {{ item.text }}
    </span>
    <span> ({{ item.shade }})</span>
  </div>
</template>

<script>
export default {
  name: 'auto-complete-item',
  props: ['item']
}
</script>
