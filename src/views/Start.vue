<template>
    <div class="home">
        <QuestionPopper
                :now-question="questions[nowQuestionIndex].q"
                :now-id="nowQuestionIndex + 1"
        />
        <h3>答对人数：{{questions[nowQuestionIndex].ACCount}}</h3>
        <button @click="onNextQuestionClicked">下一题</button>

        <CountDown
                :time="90000"
                :callback="onCountdownFinish"
                :trigger="countDownTrigger"
        />
        <div id="debug-input">
            <input id="message-input"
                   placeholder="Input message..."
                   v-model="inputMessage"
                   @keyup.enter="onSendButtonClicked"
            />
            <button @click="onSendButtonClicked">Send</button>
        </div>
    </div>
</template>

<script>
    // @ is an alias to /src
    import HelloWorld from '@/components/HelloWorld.vue';
    import CountDown from "../components/CountDown";
    import * as CQWebSocket from "cq-websocket";
    import QuestionPopper from "../components/QuestionPopper";

    export default {
        name: 'home',
        components: {
            QuestionPopper,
            CountDown,
            HelloWorld
        },
        data() {
            return {
                bot: null,
                inputMessage: "",
                countDownTrigger: false,
                isCountingDown: true,
                questions: [
                    {q: "梨子是否会女装？", a: "是", ACCount: 0},
                    {q: "让我们抵制_____， 保护黑猫！", a: "音响", ACCount: 0},
                ],
                nowQuestionIndex: 0,
                groupID: 774150811,
                userInfo: {},
            }
        },
        methods: {
            onCountdownFinish() {
                this.isCountingDown = false;
            },
            onSendButtonClicked() {
                if (this.inputMessage === "") return;
                const context = {
                    message_type: "private",
                    user_id: 398317467,
                    message: this.inputMessage,
                };
                this.bot('send_msg', context);
                this.inputMessage = "";
            },
            onNextQuestionClicked() {
                if (this.nowQuestionIndex < this.questions.length - 1) {
                    this.nowQuestionIndex++;
                    // broadcast question
                    this.broadcastQuestion();
                    // trigger new count down
                    this.countDownTrigger = true;
                    setTimeout(() => this.countDownTrigger = false, 50);
                    this.isCountingDown = true;
                }
            },
            broadcastQuestion() {
                const context = {
                    message_type: "group",
                    group_id: this.groupID,
                    message: "[CQ:face,id=13] 第" + (this.nowQuestionIndex + 1) + "题：\n"
                        + this.questions[this.nowQuestionIndex].q,
                };
                this.bot('send_msg', context);
            },
            repeatress(context) {
                // 复读姬
                context.message_type = "private";
                context.user_id = 398317467;
                this.bot('send_msg', context);
            },
            countACUser(context) {
                if (!this.isCountingDown)
                    return;
                if (this.questions[this.nowQuestionIndex].ACUserList === undefined) {
                    this.questions[this.nowQuestionIndex].ACUserList = [];
                }
                if (this.questions[this.nowQuestionIndex].a === context.message) {
                    let found = false;
                    for (let u of this.questions[this.nowQuestionIndex].ACUserList) {
                        if (u === context.user_id)
                            found = true;
                    }
                    if (!found) {
                        this.questions[this.nowQuestionIndex].ACUserList.push(context.user_id);
                        this.questions[this.nowQuestionIndex].ACCount++;
                        console.log(this.questions[this.nowQuestionIndex].ACUserList);

                        this.getUserInfo(this.groupID, context.user_id);
                    }
                }
            },
            getUserInfo(groupID, userID) {
                this.bot('get_group_member_info', {
                    group_id: groupID,
                    user_id: userID,
                    no_cache: true,
                })
                    .then(rst => {
                        console.log(rst);
                    });
            }
        },
        mounted() {
            this.bot = new CQWebSocket();
            this.bot
                .on('socket.connecting', (wsType, attempts) => {
                    console.log('尝试第 %d 次连接 _(:з」∠)_', attempts)
                })
                .on('socket.connect', (wsType, sock, attempts) => {
                    console.log('第 %d 次连接成功 ヽ(✿ﾟ▽ﾟ)ノ', attempts);
                })
                .on('socket.failed', (wsType, attempts) => {
                    console.log('第 %d 次连接失败 。･ﾟ･(つд`ﾟ)･ﾟ･', attempts)
                })
                .on('api.response', (resObj) => console.log('服务器响应: %O', resObj))
                .on('message.group', (e, context) => {
                    console.log(context);
                    this.countACUser(context);
                    e.stopPropagation();
                });
            this.bot.connect();
            // Broadcast the first question
            setTimeout(this.broadcastQuestion, 500);
        },
    }
</script>

<style scoped lang="scss">
    #debug-input {
        display: none;
    }
</style>