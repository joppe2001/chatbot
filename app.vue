<template>
  <div>
    <h1>Chat with AI</h1>
    <div class="chat-box">
      <div v-for="message in messages" :key="message.id" class="message">
        <p :class="{'user-message': message.sender === 'user', 'ai-message': message.sender === 'ai'}">
          {{ message.text }}
        </p>
      </div>
    </div>
    <div class="input-box">
      <input v-model="currentMessage" @keyup.enter="sendMessage" type="text" placeholder="Type a message...">
      <button @click="sendMessage">Send</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const messages = ref([]);
const currentMessage = ref('');

const sendMessage = async () => {
  if (!currentMessage.value.trim()) return;

  // Add user message to chat
  messages.value.push({ id: Date.now(), text: currentMessage.value, sender: 'user' });

  // Send request to Flask API
  try {
    const response = await fetch('http://127.0.0.1:5000/chat', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ input: currentMessage.value })
    });

    if (!response.ok) throw new Error('Network response was not ok.');

    const data = await response.json();
    messages.value.push({ id: Date.now(), text: data.response, sender: 'ai' });
  } catch (error) {
    console.error('Error:', error);
  }

  currentMessage.value = ''; // Reset input field
};
</script>

<style lang="scss">
$background-color: #f4f4f8;
$primary-color: #5b9bd5;
$secondary-color: #ed7d31;
$user-message-color: #d0e0f0;
$ai-message-color: #f0d0d0;
$font-family: 'Arial', sans-serif;

body {
  font-family: $font-family;
  margin: 0;
  padding: 0;
  background-color: $background-color;
}

h1 {
  color: $primary-color;
  text-align: center;
}

.chat-box {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  max-width: 600px;
  margin: 20px auto;
  padding: 10px;
  background-color: white;
  border: 1px solid $primary-color;
  border-radius: 8px;
  overflow-y: auto;
  max-height: 400px;

  .message {
    margin: 5px;
    padding: 5px;
    border-radius: 5px;

    p {
      margin: 0;
      padding: 5px;
    }

    .user-message {
      background-color: $user-message-color;
      align-self: flex-end;
    }

    .ai-message {
      background-color: $ai-message-color;
      align-self: flex-start;
    }
  }
}

.input-box {
  display: flex;
  justify-content: space-between;
  max-width: 600px;
  margin: 0 auto;

  input {
    flex-grow: 1;
    margin-right: 10px;
    padding: 10px;
    border: 1px solid $secondary-color;
    border-radius: 4px;
  }

  button {
    padding: 10px 20px;
    background-color: $primary-color;
    border: none;
    color: white;
    border-radius: 4px;
    cursor: pointer;

    &:hover {
      background-color: darken($primary-color, 10%);
    }
  }
}
</style>
