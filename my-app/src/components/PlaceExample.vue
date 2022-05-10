<template>
  <div style="margin: 17px 0px">
    {{ count }}
    <button v-on:click="count = count + 1">count</button>
  </div>
</template>

<script>
export default {
  name: "PlaceExample",
  data: function () {
    return {
      count: 0,
    };
  },
};
</script>
