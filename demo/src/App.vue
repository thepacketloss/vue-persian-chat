<template>
    <div :style="{background: backgroundColor}">
        <persian-chat
                :alwaysScrollToBottom="alwaysScrollToBottom"
                :close="closeChat"
                :colors="colors"
                :isOpen="isChatOpen"
                :messageList="messageList"
                :messageStyling="messageStyling"
                :newMessagesCount="newMessagesCount"
                :onMessageWasSent="onMessageWasSent"
                :open="openChat"
                :participants="participants"
                :showCloseButton="true"
                :showLauncher="true"
                :showEmoji="true"
                :showTypingIndicator="showTypingIndicator"
                :titleImageUrl="titleImageUrl"
                @onType="handleOnType"
                @remove="removeMessage"
                title="پشتیبانی سیستم اکانتینگ"
                disableUserListToggle>
            <template v-slot:text-message-body="scopedProps">
                <p class="sc-message--text-content" v-html="scopedProps.messageText"></p>
                <p v-if="scopedProps.message.data.meta" class='sc-message--meta'
                   :style="{color: scopedProps.messageColors.color}">{{scopedProps.message.data.meta}}</p>
            </template>
            <template v-slot:system-message-body="{ message }">
                پیام سیستم> : {{message.text}}>
            </template>
        </persian-chat>
        <p class="text-center colors">
            <a :style="{background: availableColors.blue.launcher.bg, margin:'0 5px'}" @click.prevent="setColor('blue')"
               href="#">آبی</a>
            <a :style="{background: availableColors.red.launcher.bg, margin:'0 5px'}" @click.prevent="setColor('red')"
               href="#">قرمز</a>
            <a :style="{background: availableColors.green.launcher.bg, margin:'0 5px'}"
               @click.prevent="setColor('green')"
               href="#">سبز</a>
            <a :style="{background: availableColors.dark.launcher.bg, margin:'0 5px'}" @click.prevent="setColor('dark')"
               href="#">تیره</a>
        </p>
        <v-dialog/>
        <TestArea :chosenColor="chosenColor"
                :colors="colors"
                :messageStyling="messageStyling"
                :onMessage="sendMessage"
                :onTyping="handleTyping"/>
    </div>
</template>

<script>
    import messageHistory from './messageHistory'
    import chatParticipants from './chatProfiles'
    import TestArea from './TestArea.vue'
    import availableColors from './colors'

    export default {
        name: 'app',
        components: {
            TestArea
        },
        data() {
            return {
                participants: chatParticipants,
                titleImageUrl:
                    'https://a.slack-edge.com/66f9/img/avatars-teams/ava_0001-34.png',
                messageList: messageHistory,
                newMessagesCount: 0,
                isChatOpen: false,
                showTypingIndicator: '',
                colors: null,
                availableColors,
                chosenColor: null,
                alwaysScrollToBottom: true,
                messageStyling: true,
                userIsTyping: false
            }
        },
        created() {
            this.setColor('blue')
        },
        methods: {
            sendMessage(text) {
                if (text.length > 0) {
                    this.newMessagesCount = this.isChatOpen
                        ? this.newMessagesCount
                        : this.newMessagesCount + 1
                    this.onMessageWasSent({
                        author: 'support',
                        type: 'text',
                        id: Math.random(),
                        data: {text}
                    })
                }
            },
            handleTyping(text) {
                this.showTypingIndicator =
                    text.length > 0
                        ? this.participants[this.participants.length - 1].id
                        : ''
            },
            onMessageWasSent(message) {
                this.messageList = [...this.messageList, Object.assign({}, message, {id: Math.random()})]
            },
            openChat() {
                this.isChatOpen = true
                this.newMessagesCount = 0
            },
            closeChat() {
                this.isChatOpen = false
            },
            setColor(color) {
                this.colors = this.availableColors[color]
                this.chosenColor = color
            },
            handleOnType() {
                this.$root.$emit('onType')
                this.userIsTyping = true
            },
            removeMessage(message) {
                if (confirm('Delete?')) {
                    const m = this.messageList.find(m => m.id === message.id);
                    m.type = 'system';
                    m.data.text = 'This message has been removed';
                }
            },
        },
        computed: {
            linkColor() {
                return this.chosenColor === 'dark'
                    ? this.colors.sentMessage.text
                    : this.colors.launcher.bg
            },
            backgroundColor() {
                return this.chosenColor === 'dark' ? this.colors.messageList.bg : '#fff'
            }
        },
        mounted() {
            this.messageList.forEach(x => x.liked = false);
        }
    }
</script>

<style>
    body {
        padding: 0px;
        margin: 0px;
    }

    * {
        font-family: IRANSans, Helvetica Neue, Helvetica, sans-serif;
    }

    .demo-description img {
        max-width: 500px;
    }

    .text-center {
        text-align: center;
    }

    .colors a {
        color: #fff;
        text-decoration: none;
        padding: 4px 10px;
        border-radius: 10px;
    }

    .toggle a {
        text-decoration: none;
    }
</style>
