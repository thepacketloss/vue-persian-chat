<template>
    <div class="demo-test-area--wrapper" :style="{color: textColor}">
        <form class="demo-test-area" @submit.prevent="_handleSubmit" @keyup="_handleTyping">
            <div class="demo-test-area--preamble">
                <p>برای تست یک متن ارسال کنید</p>
                <p v-if="userIsTyping">کاربر در حال نوشتن است ...</p>
            </div>
            <textarea ref="textArea" class="demo-test-area--text" :style="textareaStyle"/>
            <button class="demo-test-area--button"
                    :style="{background: ctaColor, color: colors.sentMessage.text,fontFamily:'IRANSans'}">ارسال پیام
            </button>
        </form>
    </div>
</template>
<script>
    export default {
        props: {
            onMessage: {
                type: Function,
                required: true
            },
            onTyping: {
                type: Function,
                required: true
            },
            colors: {
                type: Object,
                required: true
            },
            chosenColor: {
                type: String,
                required: true
            },
            messageStyling: {
                type: Boolean,
                required: true
            }
        },
        data() {
            return {
                userIsTyping: false
            }
        },
        methods: {
            _handleSubmit() {
                this.onMessage(this.$refs.textArea.value)
                this.$refs.textArea.value = ''
                this.onTyping('')
            },
            _handleTyping() {
                this.onTyping(this.$refs.textArea.value)
            }
        },
        computed: {
            linkColor() {
                return this.chosenColor === 'dark' ? this.colors.sentMessage.text : this.colors.launcher.bg
            },
            backgroundColor() {
                return this.chosenColor === 'dark' ? this.colors.messageList.bg : '#fff'
            },
            textColor() {
                return this.chosenColor === 'dark' ? '#eee' : '#222'
            },
            ctaColor() {
                return this.chosenColor === 'dark' ? this.colors.userInput.bg : this.colors.launcher.bg
            },
            textareaStyle() {
                return {
                    background: this.chosenColor === 'dark' ? this.colors.messageList.bg : '#fff',
                    color: this.chosenColor === 'dark' ? this.colors.sentMessage.text : '#222',
                    direction: 'rtl'
                }
            }
        },
        mounted() {
            this.$root.$on('onType', () => {
                this.userIsTyping = true
                setTimeout(() => {
                    this.userIsTyping = false
                }, 3000);
            })
        }
    }
</script>
<style scoped>
    .demo-test-area--wrapper {
        width: 100%;
        display: flex;
        flex-direction: column;
    }

    .demo-test-area {
        align-self: center;
        margin-top: 30px;
        width: 400px;
        display: flex;
        flex-direction: column;
    }

    .demo-test-area--preamble {
        padding: 20px 0px;
        text-align: center;
    }

    .demo-test-area--button {
        font-family: Avenir Next, Helvetica Neue, Helvetica, sans-serif;
        font-weight: 400;
        margin-top: 20px;
        user-select: none;
        border: none;
        line-height: 1.4;
        text-decoration: none;
        background: #4e8cff;
        color: white;
        padding: 6px 10px;
        font-size: 20px;
        height: 50px;
        border-radius: 4px;
        width: 80%;
        box-sizing: border-box;
        outline: none;
        cursor: pointer;
        align-self: center;
    }

    .demo-test-area--button:hover {
        background: #4883f1;
    }

    .demo-test-area--info a {
        font-weight: 400;
        text-decoration: none;
        color: #4e8cff;
    }
</style>
