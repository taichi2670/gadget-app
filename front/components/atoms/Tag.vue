<template>
  <v-chip
    class="ma-2"
    color="orange"
    label
    text-color="white"
    :to="`/tags/${tag.id}`"
  >
    <v-icon left>
      mdi-label
    </v-icon>
    {{ tag.tag_name }}
  </v-chip>
</template>
<script>
export default {
  props: {
    tag: {
      type: Object
    }
  }
}
</script>