<template>
  <v-container>
    <!-- <TypeWeekCalendar /> -->
    <TypeWeekDayCalendar />
  </v-container>
</template>

<script>
import TypeWeekCalendar from '@/components/TypeWeekCalendar.vue';
import TypeWeekDayCalendar from '@/components/TypeWeekDayCalendar.vue';
export default {
  name: 'IndexPage',
  components: {
    TypeWeekCalendar,
    TypeWeekDayCalendar
  },
}
</script>
