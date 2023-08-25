<template>
    <v-container>
        <DragAndDropCalendar />

    </v-container>
</template>

<script>
import DragAndDropCalendar from '@/components/DragAndDropCalendar.vue';
export default {
    name: 'IndexPage',
    components: {
        DragAndDropCalendar
    },
}
</script>
