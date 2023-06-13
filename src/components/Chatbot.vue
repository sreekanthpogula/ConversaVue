<template>
  <div class="chatbot">
    <div class="chatbox">
      <div class="chatlog">
        <div v-for="message in chatLog" :key="message.id">
          <div v-if="message.isUser" class="user-message">{{ message.text }}</div>
          <div v-else class="bot-message">{{ message.text }}</div>
        </div>
      </div>
      <div class="input-container">
        <input v-model="userInput" @keyup.enter="sendMessage" type="text" placeholder="Type your message..." />
        <button @click="sendMessage">Send</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

const apiToken = process.env.VUE_APP_API_TOKEN;

export default {
  name: "chatbotComponent",
  data() {
    return {
      chatLog: [],
      userInput: '',
      apiEndpoint: 'https://api.chatgpt.com/v1/chat',
    };
  },
  methods: {
    async sendMessage() {
      const userMessage = {
        id: Date.now(),
        text: this.userInput,
        isUser: true,
      };

      this.chatLog.push(userMessage);
      this.userInput = '';

      try {
        const response = await axios.post(this.apiEndpoint, {
          messages: [
            { role: 'system', content: 'You are a user' },
            { role: 'user', content: userMessage.text },
          ],
        }, {
          headers: {
            Authorization: `Bearer ${apiToken}`,
          },
        });

        const botMessage = {
          id: Date.now(),
          text: response.data.choices[0].message.content,
          isUser: false,
        };

        this.chatLog.push(botMessage);
      } catch (error) {
        console.error('ChatGPT API error:', error);
      }
    },
  },
};
</script>

<style scoped>
.chatbot {
  /* Add your own styles here */
}

.chatbox {
  /* Add your own styles here */
}

.chatlog {
  /* Add your own styles here */
}

.user-message {
  /* Add your own styles here */
}

.bot-message {
  /* Add your own styles here */
}

.input-container {
  /* Add your own styles here */
}
</style>
