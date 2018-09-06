<template>
    <div>
        <h1>{{remainingMinutes}}:{{remainingSeconds}}</h1>
        <h2>:{{remainingMilliseconds}}</h2>
    </div>
</template>

<script>
    export default {
        name: "CountDown",
        props: {
            time: Number,
            callback: Function,
            trigger: Boolean,
        },
        data() {
            return {
                remainingTime: 0,
                timer: null,
            }
        },
        computed: {
            remainingMinutes() {
                let s = Math.floor(this.remainingTime / 60000) + '';
                if (s.length === 1) {
                    s = '0' + s;
                }
                return s;
            },
            remainingSeconds() {
                let s = Math.floor(this.remainingTime % 60000 / 1000) + '';
                if (s.length === 1) {
                    s = '0' + s;
                }
                return s;
            },
            remainingMilliseconds() {
                let s = Math.floor(this.remainingTime % 1000) + '';
                if (s.length === 1) {
                    s = '00' + s;
                } else if (s.length === 2) {
                    s = '0' + s;
                }
                return s;
            },
        },
        methods: {
            start() {
                // Clear interval
                if (this.timer !== null)
                    clearInterval(this.timer);
                // Set initial time
                this.remainingTime = this.time;
                // Start counting down
                this.timer = setInterval(() => {
                    this.remainingTime -= 13;
                    if (this.remainingTime <= 0) {
                        this.remainingTime = 0;
                        clearInterval(this.timer);
                        this.callback();
                    }
                }, 13);
            },
        },
        mounted() {
            this.start();
        },
        watch: {
            trigger(newValue) {
                if (newValue)
                    this.start();
            }
        },
    }
</script>

<style scoped lang="scss">
    h1 {
        display: inline;
        font-size: 10em;
    }
    h2 {
        display: inline;
        font-size: 5em;
    }
</style>