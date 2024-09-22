<template>
    <div class="chat-container">
        <h3>Chat with Sakshamapp AI</h3>
        <div class="chat-box" ref="chatBox">
            <div v-for="(message, index) in messages" :key="index" :class="message.role">
                <p v-html="renderMarkdown(message.text)"></p>
            </div>
        </div>
        <input v-model="userInput" @keyup.enter="sendMessage" placeholder="Type your message..." />
        <br />
        <br />
        <button @click="sendMessage">Send</button>
    </div>
</template>

<script>
import axios from 'axios'; 
import { marked } from 'marked';

export default {
    data() {
        return {
            userInput: '',
            messages: [
                { role: 'model', text: 'Great to meet you. What would you like to know?' }
            ]
        };
    },
    methods: {
        async sendMessage() {
            if (this.userInput.trim() === '') return;

            this.messages.push({ role: 'user', text: this.userInput });

            try {
                const response = await axios.post('http://34.135.43.13/chat', {
                    userInput: this.userInput
                });

                this.messages.push({ role: 'model', text:  response.data.response });
            } catch (error) {
                console.error('Error sending message:', error);
            }

            this.userInput = '';
            this.scrollToBottom();
        },
        scrollToBottom() {
            this.$nextTick(() => {
                const chatBox = this.$refs.chatBox;
                chatBox.scrollTop = chatBox.scrollHeight;
            });
        },
        renderMarkdown(text) {
            return marked(text);
        }
    }
};
</script>

<style>
.chat-container {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
    border: 1px solid #ccc;
    border-radius: 10px;
}

.chat-box {
    max-height: 400px;
    overflow-y: auto;
    margin-bottom: 20px;
    border: 1px solid #ccc;
    padding: 10px;
    border-radius: 10px;
}

.user {
    text-align: right;
    color: blue;
}

.model {
    text-align: left;
    color: green;
}

input {
    width: calc(100% - 60px);
    padding: 10px;
    margin-right: 10px;
}

button {
    padding: 10px;
}
</style>