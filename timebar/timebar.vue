<template>
    <div>
        <span class="font-weight-light">{{prefix}}</span>
        <span class="font-weight-bold">{{time}}</span>
        <span class="font-weight-light">{{suffix}}</span>
    </div>
</template>

<script>
    import moment from "moment";

    export default {
        name: "timebar",
        data: () => ({
            timeNow: moment()
        }),
        mounted() {
            setInterval(() => {
                this.timeNow = moment();
            }, this.counter);
        },
        computed: {
            time: function() {
                return this.timeNow.format(this.format);
            }
        },
        props: {
            prefix: {
                    type: String,
                    default() { return ""; }
            },
            suffix: {
                type: String,
                default() { return ""; }
            },
            format: {
                type: String,
                default() { return "hh:mm:ss A"; }
            },
            counter: {
                type: Number,
                required: true
            }
        }
    }
</script>

<style scoped>

</style>