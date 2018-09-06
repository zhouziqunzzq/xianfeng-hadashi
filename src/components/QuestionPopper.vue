<template>
    <div>
        <h2>{{nowId}}. {{nowQuestion}}</h2>
    </div>
</template>

<script>
    export default {
        name: "QuestionPopper",
        props: {
            nowQuestion: String,
            nowId: Number,
        },
    }
</script>

<style scoped lang="scss">

</style>