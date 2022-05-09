<template>
  <div style="margin: 17px 0px">
    <div>{{ textValue.first }}</div>
    <div v-bind:style="placeStyle">{{ textValue.last }}</div>
  </div>
</template>

<script>
export default {
  name: "PlaceTable",
  props: {
    textValue: {
      type: Object,
      default: function () {
        return {};
      },
    },
  },
  computed: {
    placeStyle: function () {
      return this.textValue.isPlace ? { color: "red" } : { color: "#111111" };
    },
  },
};
</script>
