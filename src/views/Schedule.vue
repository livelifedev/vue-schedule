<template>
  <div>
    <router-link to="/">Home</router-link>
    <h1>{{ $route.params.id }}</h1>
    <div v-for="(slots, name) in timeslots" :key="name">
      <h3>{{ name }}</h3>
      <ol>
        <li v-for="time in slots" :key="time">{{ time }}</li>
      </ol>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Schedule',

  data() {
    return {
      data: [],
    };
  },
  computed: {
    timeslots() {
      const availabilityMap = {};

      // Does not account for duplicates and gaps in time
      this.data.forEach((details) => {
        if (details.name.includes(this.$route.params.id)) {
          availabilityMap[details.day_of_week] = this.generateSlots(
            details.available_at,
            details.available_until
          );
        }
      });

      return availabilityMap;
    },
  },

  async created() {
    const res = await fetch(
      'https://raw.githubusercontent.com/suyogshiftcare/jsontest/main/available.json'
    );

    this.data = await res.json();
  },

  methods: {
    convertTimeToNumber(time) {
      const hourMins = time.split(':');
      let timeAsNumber;

      // Set initially with the hour value
      timeAsNumber = parseInt(hourMins[0]);

      // Adjust for afternoon/night
      if (time.includes('PM')) {
        timeAsNumber += 12;
      }

      // Check time if on the hour or half past
      if (hourMins[1][0] > 0) {
        timeAsNumber += 0.5;
      }

      return timeAsNumber;
    },

    getDifference(startTime, endTime) {
      return (
        this.convertTimeToNumber(endTime) - this.convertTimeToNumber(startTime)
      );
    },

    generateSlots(startTime, endTime) {
      const timeslots = [];
      let start = this.convertTimeToNumber(startTime);
      let end = this.convertTimeToNumber(endTime);

      while (start < end) {
        timeslots.push(start);
        start += 0.5;
      }

      return timeslots;
    },
  },
};
</script>

<style scoped></style>
