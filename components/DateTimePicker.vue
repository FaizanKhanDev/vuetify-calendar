<template>
    <div>
        <v-menu v-model="dialog" :close-on-content-click="false" :nudge-width="200" max-width="290" offset-y
            :disabled="disabled">
            <template v-slot:activator="{ on }">
                <v-text-field v-model="value" :label="label" v-bind="$attrs" v-on="on" readonly
                    :disabled="disabled"></v-text-field>
                <VMessages :value="errorBucket" color="error" />
            </template>
            <template v-slot:default="dialog">
                <v-card>
                    <v-tabs v-model="tabs" fixed-tabs>
                        <v-tabs-slider></v-tabs-slider>
                        <v-tab href="#pick-date" class="primary--text">
                            <v-icon>mdi-calendar</v-icon>
                        </v-tab>

                        <v-tab href="#pick-time" class="primary--text" v-if="datePicker">
                            <v-icon>mdi-clock</v-icon>
                        </v-tab>
                    </v-tabs>
                    <v-tabs-items v-model="tabs">
                        <v-tab-item value="pick-date">
                            <v-date-picker v-model="datePicker" :rules="rules.eventDateRule"></v-date-picker>
                        </v-tab-item>
                        <v-tab-item value="pick-time">
                            <v-time-picker format="ampm" v-model="timePicker" scrollable></v-time-picker>
                        </v-tab-item>
                    </v-tabs-items>
                    <v-card-text> </v-card-text>
                    <v-card-actions class="justify-end">
                        <v-btn text @click="clear()" v-if="clearable">Clear</v-btn>
                        <v-btn text @click="dialog.value = false">OK</v-btn>
                    </v-card-actions>
                </v-card>
            </template>
        </v-menu>
    </div>
</template>

<script>
import VInput from "vuetify/lib/components/VInput/VInput.js";

export default {
    extends: VInput,
    data() {
        return {
            dialog: false,
            datePicker: "",
            timePicker: "",
            tabs: null,
        };
    },
    props: {
        value: {
            required: false,
            type: [String],
        },
        required: {
            required: false,
            type: [Boolean],
        },
        label: {
            required: false,
            type: [String],
        },
        clearable: {
            required: false,
            type: [Boolean],
        },
        preset: {
            required: false,
            type: [Boolean],
        },
        disabled: {
            required: false,
            type: [Boolean],
        },
        rule: {
            require: true,
            type: [Object],
        },
    },
    watch: {
        datePicker: function () {
            this.$emit("input", this.datePicker + " " + this.timePicker);
            this.$set(this, "tabs", "pick-time");
        },
        timePicker: function () {
            this.$emit("input", this.datePicker + " " + this.timePicker);
        },
        clear: function () {
            this.$set(this, "datePicker", null);
            this.$set(this, "timePicker", null);
            this.$emit("input", null);
        },
    },
    methods: {
        clear() {
            this.datePicker = "";
            this.timePicker = "";
            this.$emit("input", null);
        },
    },
    mounted() {
        if (this.preset) {
            const time = new Date().toLocaleTimeString("en-US", {
                hour12: false,
                hour: "numeric",
                minute: "numeric",
            });
            const date = new Date().toISOString().substr(0, 10);
            this.$set(this, "datePicker", date);
            this.$set(this, "timePicker", time);
            this.$emit("input", date + " " + time);
        }
    },
};
</script>