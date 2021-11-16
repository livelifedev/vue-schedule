<template>
  <div>
    <ul>
      <li v-for="(coach, index) in coaches" :key="index">
        <router-link :to="`/schedule/${coach}`">
          {{ coach }}
        </router-link>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'Home',

  data() {
    return {
      data: [],
    };
  },
  computed: {
    coaches() {
      return this.data.reduce((prev, curr) => {
        if (!prev.includes(curr.name)) {
          prev.push(curr.name);
        }
        return prev;
      }, []);
    },
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
