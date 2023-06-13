<template>
  <div class="chatbot">
    <div class="chatbox">
      <transition-group name="fade">
        <div v-for="message in chatLog" :key="message.id">
          <div
            v-if="message.isUser"
            class="user-message animated fadeInRight"
            @click="toggleVisibility(message)"
          >
            {{ message.text }}
          </div>
          <div
            v-else
            class="bot-message animated fadeInLeft"
            @click="toggleVisibility(message)"
          >
            {{ message.text }}
          </div>
        </div>
      </transition-group>
      <div class="input-container">
        <input v-model="userInput" @keyup.enter="sendMessage" type="text" placeholder="Type your message..." />
        <v-btn @click="sendMessage" color="primary">Send</v-btn>
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
      apiEndpoint: 'https://api.chatgpt.com/v1/chat/completions',
    };
  },
  methods: {
    async sendMessage() {
      const userMessage = {
        id: Date.now(),
        text: this.userInput,
        isUser: true,
        visible: true,
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
          visible: true,
        };

        this.chatLog.push(botMessage);
      } catch (error) {
        console.error('ChatGPT API error:', error);
      }
    },
    toggleVisibility(message) {
      message.visible = !message.visible;
    },
  },
};
</script>

<style scoped>
.chatbot {
  /* Add your own styles here */
  background-color: #f2f2f2;
  border-radius: 10px;
  padding: 20px;
}

.chatbox {
  /* Add your own styles here */
  max-width: 600px;
  margin: 0 auto;
}

.chatlog {
  /* Add your own styles here */
  margin-bottom: 20px;
}

.user-message {
  /* Add your own styles here */
  background-color: #cce6ff;
  border-radius: 5px;
  padding: 10px;
  margin-bottom: 10px;
}

.bot-message {
  /* Add your own styles here */
  background-color: #f0f0f0;
  border-radius: 5px;
  padding: 10px;
  margin-bottom: 10px;
}

.input-container {
  /* Add your own styles here */
  display: flex;
  align-items: center;
  margin-top: 10px;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}

.animated {
  animation-duration: 0.5s;
}

@keyframes fadeInLeft {
  from {
    opacity: 0;
    transform: translateX(-10px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@keyframes fadeInRight {
  from {
    opacity: 0;
    transform: translateX(10px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

.fadeInLeft {
  animation-name: fadeInLeft;
}

.fadeInRight {
  animation-name: fadeInRight;
}
</style>
