<script setup>
import { io } from 'socket.io-client';
import { onBeforeMount, ref } from 'vue';
const socket = io('http://localhost:3001');
const messages = ref([]);
const messageText = ref('');
const joined = ref(false);
const name = ref('');

onBeforeMount(() => {
  socket.emit('findAllMessages', {}, (response) => {
    messages.value = response;
  })

  socket.on('message', (message) => {
    messages.value.push(message)
  })
})

  const join = () => {
    console.log('joining')
    socket.emit('join', {name: name.value}, () => {
      joined.value = true;
    })
  }

  const sendMessage = () => {
    socket.emit('createMessage', {message: messageText.value}, (reponse) => {
      messageText.value = '';
    })
  }



</script>

<template>
  <div class="chat">
    <div v-if="!joined">
      <form @submit.prevent="join">
        <label for="name">What's your name?</label>
        <input type="text" v-model="name"/>
        <button type="submit">Join</button>
      </form>
    </div>
    <div class="chat-container" v-else>
      Chat
      <div v-for="message in messages">
        [{{ message.name }}]: {{message.message}}
      </div>
      <div class="message-field">
        <form @submit.prevent="sendMessage">
          <input type="text" v-model="messageText" />
          <button type="submit">Send</button>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>
