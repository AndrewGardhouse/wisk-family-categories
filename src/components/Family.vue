<template>
  <div>
    <h5 class="text-left m-3">{{ family.title }}</h5>
  </div>
</template>

<script>
export default {
  props: {
    family: {
      type: Object,
      required: true
    }
  }
}
</script>

<style lang="css">
</style>
