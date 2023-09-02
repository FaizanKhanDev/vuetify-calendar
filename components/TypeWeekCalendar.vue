<template>
    <v-row class="fill-height">
        <v-col>
            <v-sheet height="64">
                <v-toolbar flat>
                    <v-btn outlined class="mr-4" color="grey darken-2" @click="setToday">
                        Today
                    </v-btn>
                    <v-btn fab text small color="grey darken-2" @click="prev">
                        <v-icon small>
                            mdi-chevron-left
                        </v-icon>
                    </v-btn>
                    <v-btn fab text small color="grey darken-2" @click="next">
                        <v-icon small>
                            mdi-chevron-right
                        </v-icon>
                    </v-btn>
                    <v-toolbar-title v-if="$refs.calendar">
                        {{ $refs.calendar.title }}
                    </v-toolbar-title>
                    <v-spacer></v-spacer>
                    <v-menu bottom right>
                        <template v-slot:activator="{ on, attrs }">
                            <v-btn outlined color="grey darken-2" v-bind="attrs" v-on="on">
                                <span>{{ typeToLabel[type] }}</span>
                                <v-icon right>
                                    mdi-menu-down
                                </v-icon>
                            </v-btn>
                        </template>
                        <v-list>
                            <v-list-item @click="type = 'day'">
                                <v-list-item-title>Day</v-list-item-title>
                            </v-list-item>
                            <v-list-item @click="type = 'week'">
                                <v-list-item-title>Week</v-list-item-title>
                            </v-list-item>
                            <v-list-item @click="type = 'month'">
                                <v-list-item-title>Month</v-list-item-title>
                            </v-list-item>
                            <v-list-item @click="type = '4day'">
                                <v-list-item-title>4 days</v-list-item-title>
                            </v-list-item>
                        </v-list>
                        <v-btn fab small class="ml-1" @click.stop="dialog = true">
                            <v-icon> mdi-plus </v-icon>
                        </v-btn>
                    </v-menu>
                </v-toolbar>
            </v-sheet>
            <v-sheet height="600">
                <v-calendar ref="calendar" v-model="focus" color="primary" :events="events" :event-color="getEventColor"
                    :type="type" @click:event="showEvent" @click:more="viewDay" @click:date="viewDay" @change="updateRange"
                    @mousedown:event="startDrag" @mousedown:time="startTime" @mousemove:time="mouseMove"
                    @mouseup:time="endDrag" @mouseleave.native="cancelDrag" :event-ripple="false">
                    <template v-slot:event="{ event, timed, eventSummary }">
                        <div class="v-event-draggable">
                            <component :is="{ render: eventSummary }"></component>
                        </div>
                        <div v-if="timed" class="v-event-drag-bottom" @mousedown.stop="extendBottom(event)"></div>
                    </template>
                </v-calendar>


                <v-menu v-model="selectedOpen" :close-on-content-click="false" :activator="selectedElement" offset-x>
                    <v-card color="grey lighten-4" min-width="350px" flat>
                        <v-toolbar :color="selectedEvent.color" dark>
                            <v-btn icon @click="editEvent(selectedEvent)">
                                <v-icon>mdi-pencil</v-icon>
                            </v-btn>
                            <v-toolbar-title v-html="selectedEvent.name"></v-toolbar-title>
                            <v-spacer></v-spacer>
                            <!-- <v-btn icon>
                                <v-icon>mdi-heart</v-icon>
                            </v-btn> -->
                            <v-btn icon>
                                <v-icon>mdi-dots-vertical</v-icon>
                            </v-btn>
                        </v-toolbar>
                        <v-card-text>
                            <span v-html="selectedEvent.detail"></span>
                        </v-card-text>
                        <v-card-actions>
                            <v-btn text color="secondary" @click="selectedOpen = false">
                                Cancel
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </v-menu>
            </v-sheet>
        </v-col>
        <!--  Add Event Dialog -->
        <!-- Inside the Add Event Dialog -->
        <v-dialog v-model="dialog" persistent max-width="500">
            <v-card>
                <!-- ... card content ... -->
                <v-form @submit.prevent="addEvent">
                    <v-card-text>
                        <v-container>
                            <!-- Name -->
                            <v-row>
                                <v-col cols="12" sm="12" md="12">
                                    <v-text-field v-model="name" type="text" label="Event Name (required)"
                                        required></v-text-field>
                                </v-col>
                            </v-row>
                            <!-- Detail -->
                            <v-row>
                                <v-col cols="12">
                                    <v-textarea v-model="detail" solo required outlined auto-grow rows="3"
                                        label="Add event details (required)"></v-textarea>
                                </v-col>
                            </v-row>
                            <!-- Start and End Date Time Picker -->
                            <v-row>
                                <!-- Start Date Date Picker -->
                                <v-col cols="12" sm="6" md="6">
                                    <DateTimePicker label="Start Date & Time" v-model="start" :required="true"
                                        :preset="true" :clearable="true"></DateTimePicker>
                                </v-col>
                                <!-- End Date Date Picker -->
                                <v-col cols="12" sm="6" md="6">
                                    <DateTimePicker label="End Date & Time" v-model="end" :required="true" :preset="true"
                                        :clearable="true"></DateTimePicker>
                                </v-col>
                            </v-row>
                            <!-- Color Picker -->
                            <v-row>
                                <v-col cols="12">
                                    <v-col class="d-flex justify-center">
                                        <div>
                                            <v-label>Event Color (required)</v-label>
                                        </div>
                                        <v-color-picker v-model="color" hide-inputs canvas-height="120"
                                            width="300"></v-color-picker>
                                    </v-col>
                                </v-col>
                            </v-row>
                        </v-container>
                    </v-card-text>
                    <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn text @click="dialog = false">Close</v-btn>
                        <v-btn type="submit">Create Event</v-btn>
                    </v-card-actions>
                </v-form>
            </v-card>
        </v-dialog>

        <!--  edite the Selected Event -->
        <v-dialog v-model="editDialog" persistent max-width="500">
            <v-card>
                <!-- ... card content ... -->
                <v-form @submit.prevent="saveEvent">
                    <v-card-text>
                        <v-container>
                            <!-- Name -->
                            <v-row>
                                <v-col cols="12" sm="12" md="12">
                                    <v-text-field v-model="selectedEvent.name" type="text" label="Event Name (required)"
                                        required></v-text-field>
                                </v-col>
                            </v-row>
                            <!-- Detail -->
                            <v-row>
                                <v-col cols="12">
                                    <v-textarea v-model="selectedEvent.detail" solo required outlined auto-grow rows="3"
                                        label="Add event details (required)"></v-textarea>
                                </v-col>
                            </v-row>
                            <!-- Start and End Date Time Picker -->
                            <v-row>
                                <!-- Start Date Date Picker -->
                                <v-col cols="12" sm="6" md="6">
                                    <DateTimePicker label="Start Date & Time" v-model="selectedEvent.start" :required="true"
                                        :preset="true" :clearable="true"></DateTimePicker>
                                </v-col>
                                <!-- End Date Date Picker -->
                                <v-col cols="12" sm="6" md="6">
                                    <DateTimePicker label="End Date & Time" v-model="selectedEvent.end" :required="true"
                                        :preset="true" :clearable="true"></DateTimePicker>
                                </v-col>
                            </v-row>
                            <!-- Color Picker -->
                            <v-row>
                                <v-col cols="12">
                                    <v-col class="d-flex justify-center">
                                        <div>
                                            <v-label>Event Color (required)</v-label>
                                        </div>
                                        <v-color-picker v-model="selectedEvent.color" hide-inputs canvas-height="120"
                                            width="300"></v-color-picker>
                                    </v-col>
                                </v-col>
                            </v-row>
                        </v-container>
                    </v-card-text>
                    <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn text @click="editDialog = false">Close</v-btn>
                        <v-btn type="submit">Save Event</v-btn>
                    </v-card-actions>
                </v-form>
            </v-card>
        </v-dialog>

    </v-row>
</template>
<script>
import DateTimePicker from './DateTimePicker.vue'
export default {
    components: {
        DateTimePicker
    },
    data: () => ({
        focus: '',
        type: 'month',
        typeToLabel: {
            month: 'Month',
            week: 'Week',
            day: 'Day',
            '4day': '4 Days',
        },
        selectedEvent: {},
        selectedElement: null,
        dialog: false,
        selectedOpen: false,
        editDialog: false,
        events: [


        ],
        colors: ['blue', 'indigo', 'deep-purple', 'cyan', 'green', 'orange', 'grey darken-1'],
        names: ['Meeting', 'Holiday', 'PTO', 'Travel', 'Event', 'Birthday', 'Conference', 'Party'],
        name: null,
        detail: null,
        start: null,
        end: null,
        color: "#1976D2",
        dragEvent: null,
        dragStart: null,
        createEvent: null,
        createStart: null,
        extendOriginal: null,
        selectedEvent: {
            id: null,
            name: "",
            detail: "",
            start: "",
            end: "",
            color: "",
        },
    }),
    mounted() {
        this.$refs.calendar.checkChange()
    },
    methods: {
        startDrag({ event, timed }) {
            if (event && timed) {
                this.dragEvent = event
                this.dragTime = null
                this.extendOriginal = null
            }
            console.log("fired")
        },

        startTime(tms) {
            console.log(tms)
            const mouse = this.toTime(tms)

            if (this.dragEvent && this.dragTime === null) {
                const start = this.dragEvent.start

                this.dragTime = mouse - start
                console.log("startTime", JSON.stringify(this.dragEvent))

            } else {
                this.createStart = this.roundTime(mouse)
                this.createEvent = {
                    name: `Event #${this.events.length}`,
                    color: this.rndElement(this.colors),
                    start: this.createStart,
                    end: this.createStart,
                    timed: true,
                }

                this.events.push(this.createEvent)
            }
        },

        mouseMove(tms) {
            const mouse = this.toTime(tms)

            if (this.dragEvent && this.dragTime !== null) {
                const start = this.dragEvent.start
                const end = this.dragEvent.end
                const duration = end - start
                const newStartTime = mouse - this.dragTime
                const newStart = this.roundTime(newStartTime)
                const newEnd = newStart + duration

                this.dragEvent.start = newStart
                this.dragEvent.end = newEnd
            } else if (this.createEvent && this.createStart !== null) {
                const mouseRounded = this.roundTime(mouse, false)
                const min = Math.min(mouseRounded, this.createStart)
                const max = Math.max(mouseRounded, this.createStart)

                this.createEvent.start = min
                this.createEvent.end = max
            }
        },

        endDrag() {
            this.dragTime = null
            this.dragEvent = null
            this.createEvent = null
            this.createStart = null
            this.extendOriginal = null
        },

        cancelDrag() {
            if (this.createEvent) {
                if (this.extendOriginal) {
                    this.createEvent.end = this.extendOriginal
                } else {
                    const i = this.events.indexOf(this.createEvent)
                    if (i !== -1) {
                        this.events.splice(i, 1)
                    }
                }
            }

            this.createEvent = null
            this.createStart = null
            this.dragTime = null
            this.dragEvent = null
        },


        roundTime(time, down = true) {
            const roundTo = 15 // minutes
            const roundDownTime = roundTo * 60 * 1000

            return down
                ? time - time % roundDownTime
                : time + (roundDownTime - (time % roundDownTime))
        },
        toTime(tms) {
            return new Date(tms.year, tms.month - 1, tms.day, tms.hour, tms.minute).getTime()
        },

        extendBottom(event) {
            this.createEvent = event
            this.createStart = event.start
            this.extendOriginal = event.end
        },

        viewDay({ date }) {
            this.focus = date
            this.type = 'day'
        },
        getEventColor(event) {
            return event.color
        },
        setToday() {
            this.focus = ''
        },
        prev() {
            this.$refs.calendar.prev()
        },
        next() {
            this.$refs.calendar.next()
        },
        showEvent({ nativeEvent, event }) {
            const open = () => {
                this.selectedEvent = event
                this.selectedElement = nativeEvent.target
                requestAnimationFrame(() => requestAnimationFrame(() => this.selectedOpen = true))
            }

            if (this.selectedOpen) {
                this.selectedOpen = false
                requestAnimationFrame(() => requestAnimationFrame(() => open()))
            } else {
                open()
            }

            nativeEvent.stopPropagation()
        },
        updateRange({ start, end }) {
            const events = []

            const min = new Date(`${start.date}T00:00:00`)
            const max = new Date(`${end.date}T23:59:59`)
            const days = (max.getTime() - min.getTime()) / 86400000
            const eventCount = this.rnd(days, days + 20)

            for (let i = 0; i < eventCount; i++) {
                const allDay = this.rnd(0, 3) === 0
                const firstTimestamp = this.rnd(min.getTime(), max.getTime())
                const first = new Date(firstTimestamp - (firstTimestamp % 900000))
                const secondTimestamp = this.rnd(2, allDay ? 288 : 8) * 900000
                const second = new Date(first.getTime() + secondTimestamp)

                events.push({
                    name: this.names[this.rnd(0, this.names.length - 1)],
                    start: first,
                    end: second,
                    color: this.colors[this.rnd(0, this.colors.length - 1)],
                    timed: !allDay,
                })
            }

            this.events = events
            console.log("event", JSON.stringify(this.events))

        },
        rnd(a, b) {
            return Math.floor((b - a + 1) * Math.random()) + a
        },
        async addEvent() {
            if (this.name && this.detail && this.start && this.end && this.color) {
                const newEvent = {
                    name: this.name,
                    detail: this.detail,
                    start: this.start,
                    end: this.end,
                    color: this.color,
                };
                this.events.push(newEvent);


                this.name = this.detail = this.start = this.end = this.color = "";
                this.dialog = false;
            }
            else {
                alert("Event information missing. Please fill in all required fields.");
            }
        },
        editEvent(events) {
            console.log(JSON.stringify(events))
            this.editDialog = true
        },
        saveEvent() {
            console.log("saveEvent", JSON.stringify(this.selectedEvent))
            const index = this.events.findIndex((event) => event.id === this.selectedEvent.id)
            console.log("saveEvent index", JSON.stringify(index))
            // if (index !== -1) {
            //     this.events[index] = this.selectedEvent
            //     // Reseting selected Event
            // }
            this.selectedEvent = {};
            this.editDialog = false
        }
    },

}
</script>
<style scoped lang="scss">
.v-event-draggable {
    padding-left: 6px;
}

.v-event-timed {
    user-select: none;
    -webkit-user-select: none;
}

.v-event-drag-bottom {
    position: absolute;
    left: 0;
    right: 0;
    bottom: 4px;
    height: 4px;
    cursor: ns-resize;

    &::after {
        display: none;
        position: absolute;
        left: 50%;
        height: 4px;
        border-top: 1px solid white;
        border-bottom: 1px solid white;
        width: 16px;
        margin-left: -8px;
        opacity: 0.8;
        content: '';
    }

    &:hover::after {
        display: block;
    }
}
</style>