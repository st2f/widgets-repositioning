<template>
  <div class="p-2" style="">
    <div class="card h-100 text-white bg-primary mb-3" style="">
      <div class="card-header">Header</div>
      <div class="card-body ">
        <h5 class="card-title">Primary card title</h5>
        <p class="card-text">
          <strong>{{ box.style }}</strong>
          {{ box.text }}
        </p>
      </div>
    </div>

  </div>
</template>



<script setup lang="ts">

const props = defineProps({
  box: Object
})

</script>
