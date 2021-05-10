<template>
  <div
    v-editable="blok"
    class="py-2 flex flex-col justify-center items-center"
  >
    <img 
      class="max-auto"
      :src="blok.icon"
    />
    <h1 class="text-lg my-2">{{ blok.name }}</h1>
    <p>{{ blok.description }}</p>
  </div>
</template>

<script>
export default {
  props: {
    blok: {
      type: Object,
      required: true
    }
  }
}
</script>