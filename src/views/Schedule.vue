<template>
  <div>
    <div v-show="hasBooked" class="banner">Session booked!</div>

    <router-link to="/">Home</router-link>

    <h1>{{ selectedCoach }}</h1>
    <h2>Book a Session</h2>
    <p>({{ timezone }} Local Time)</p>

    <div class="timetable">
      <section v-for="(day, dayKey) in timeslots" :key="dayKey">
        <h3>{{ dayKey }}</h3>
        <ol>
          <li v-for="time in day" :key="time">
            <button
              :class="{ active: checkIfBooked(time, dayKey) }"
              @click="setBooking(time, dayKey)"
            >
              {{ time }}
            </button>
          </li>
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

      hasBooked: false,
    };
  },
  computed: {
    selectedCoach() {
      return this.$route.params.id;
    },

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
        if (details.name === this.selectedCoach) {
          availabilityMap[details.day_of_week] = this.generateSlots(
            details.available_at,
            details.available_until
          );
        }
      });

      return availabilityMap;
    },

    timezone() {
      const coach = this.data.find(
        (details) => details.name === this.selectedCoach
      );
      return coach && coach.timezone;
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

    convertNumberToTime(number) {
      let time;
      time = number.toString();

      // Convert half hour decimal to minutes
      if (time.includes('.5')) {
        return time.split('.')[0] + ':30';
      }
      return time + ':00';
    },

    generateSlots(startTime, endTime) {
      const timeslots = [];
      let start = this.convertTimeToNumber(startTime);
      let end = this.convertTimeToNumber(endTime);

      while (start <= end) {
        const time = this.convertNumberToTime(start);
        timeslots.push(time);
        start += 0.5;
      }

      return timeslots;
    },

    setBooking(time, day) {
      this.hasBooked = false;

      const booking = {
        name: this.selectedCoach,
        time,
        day,
      };
      localStorage.setItem('booking', JSON.stringify(booking));

      this.hasBooked = true;
    },

    getBooking() {
      return JSON.parse(localStorage.getItem('booking'));
    },

    checkIfBooked(time, day) {
      const booking = this.getBooking();

      if (booking) {
        return (
          this.selectedCoach === booking.name &&
          time === booking.time &&
          day === booking.day
        );
      }
      return false;
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
  padding: 0.5rem;
}

.timetable button {
  width: 100%;
  padding: 0.5rem 1rem;
  border: none;
}

.timetable {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
}

.active {
  background-color: turquoise;
}

.banner {
  position: absolute;
  top: 0;
  width: 100%;
  padding: 1rem;
  background: turquoise;
  font-weight: bold;
}
</style>
