<template>
    <div class="container">
        <img class="inline" v-show="this.qq" :src="avatarUrl"/>
        <div class="inline">
            <h1>{{nickname}}</h1>
            <h3 v-show="this.qq">{{qq}}</h3>
        </div>
    </div>
</template>

<script>
    export default {
        name: "QQUserPopper",
        props: {
            nickname: String,
            qq: Number,
        },
        computed: {
            avatarUrl() {
                return "https://q1.qlogo.cn/g?b=qq&nk=" + this.qq + "&s=0"
            },
        },
    }
</script>

<style scoped lang="scss">
    .container {
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: center;
        margin: 1em 0;
    }

    img {
        -webkit-border-radius: 50%;
        -moz-border-radius: 50%;
        border-radius: 50%;
        width: 20%;
    }

    .inline {
        display: inline-flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        margin: 0 2em;
        -webkit-box-sizing: border-box;
        -moz-box-sizing: border-box;
        box-sizing: border-box;
    }

    h1 {
        font-size: 5em;
        margin: 0.2em 0 0 0;
    }

    h3 {
        margin: 0 0 2em 0;
    }
</style>