<template>
  <div>{{ data }}</div>
</template>

<script>
export default {
  name: 'Home',
  props: {},
  data() {
    return {
      data: [],
    };
  },

  async created() {
    const res = await fetch(
      'https://raw.githubusercontent.com/suyogshiftcare/jsontest/main/available.json'
    );

    this.data = await res.json();
  },

  methods: {},
};
</script>

<style scoped></style>
