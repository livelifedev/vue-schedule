<template>
  <div>
    <router-link to="/">Home</router-link>

    <h1>{{ $route.params.id }}</h1>

    <div class="timetable">
      <section v-for="(day, name) in timeslots" :key="name">
        <h3>{{ name }}</h3>
        <ol>
          <li v-for="time in day" :key="time">{{ time }}</li>
        </ol>
      </section>
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
      const availabilityMap = {
        Sunday: [],
        Monday: [],
        Tuesday: [],
        Wednesday: [],
        Thursday: [],
        Friday: [],
        Saturday: [],
      };

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
      if (time.includes('PM') && timeAsNumber != 12) {
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

      while (start <= end) {
        timeslots.push(start);
        start += 0.5;
      }

      return timeslots;
    },
  },
};
</script>

<style scoped>
.timetable ol {
  list-style: none;
  padding: 0;
}

.timetable li {
  padding: 1rem 0.5rem;
}

.timetable {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
}
</style>
