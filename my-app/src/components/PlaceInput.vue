<template>
  <div>
    <form style="display: flex" v-on:submit="loadInputs">
      <input
        type="text"
        style="
          width: 100%;
          padding: 17px 17px;
          background-color: #111111;
          color: #ffffff;
        "
        v-model="value"
      />
    </form>
  </div>
</template>

<script>
export default {
  name: "PlaceInput",
  props: {
    placeCustomerInput: {
      type: String,
      default: "",
    },
    placeSetCustomerInput: {
      type: Function,
      default: function (places) {
        return places;
      },
    },
    placeLoadInputs: {
      type: Function,
      default: function () {
        return {};
      },
    },
  },
  data: function () {
    return {
      value: this.placeCustomerInput,
    };
  },
  watch: {
    value: function (places) {
      this.placeSetCustomerInput(places);
    },
  },
  methods: {
    loadInputs: function (e) {
      e.preventDefault();
      this.placeLoadInputs();
      this.value = "";
    },
  },
};
</script>
