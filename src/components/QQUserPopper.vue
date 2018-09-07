<template>
    <div>
        <h3>昵称：{{nickname}}</h3>
        <h3 v-show="this.qq">QQ：{{qq}}</h3>
    </div>
</template>

<script>
    export default {
        name: "QQUserPopper",
        props: {
            nickname: String,
            qq: Number,
        }
    }
</script>

<style scoped lang="scss">

</style>