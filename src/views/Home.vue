<template>
  <div class="home">
    <ul class="list">
      <li v-for="i in 5" :key="i">{{ i }}</li>
    </ul>
    <div class="chat">
      <div class="chat-div">
        <div v-for="(item, idx) in messageList" :key="idx" class="box">
          <div :class="{ 'chat-send': item.type, 'chat-receive': !item.type }">
            {{ item.data }}
          </div>
        </div>
      </div>
      <div class="chat-input">
        <textarea v-model="inputMessage" @keyup.enter="send"></textarea>
        <button @click="send">send</button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import io from 'socket.io-client'
import { reactive, ref } from "vue";

const client = io('http://127.0.0.1:9892').connect();

const messageList = reactive([]);
client.on('message', (res: string) => {
  messageList.push({ type: 0, data: res });
});

const inputMessage = ref('');
const send = () => {

  if (inputMessage.value.trim() === "") {
    alert("no");
    inputMessage.value = "";   //防止下次从换行开始

    return;
  }
  messageList.push({ type: 1, data: inputMessage.value });
  client.emit('message', inputMessage.value);
  inputMessage.value = "";
}
</script>

<style scoped lang="scss">
.home {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;

  .list {
    width: 20rem;
    height: 596px;
    overflow-y: auto;
    background-color: #f5f7fa;

    li {
      padding: 1.2rem 2rem;
      border-bottom: 1px solid #ddd;

      &:hover {
        background-color: #8fd3f4;
      }
    }
  }
}

.chat-div {
  margin: 0 auto;
  padding: 1rem;
  height: 500px;
  width: 500px;
  // display: flex;
  flex-direction: column;
  border: solid 1px #ddd;
  overflow: auto;

  .box {
    overflow: hidden;
  }
}

.chat-input {
  margin: 0 auto;

  width: 532px;
  display: flex;

  textarea {
    flex-grow: 1;
    padding: 1rem .5rem;
    border-color: #ddd;
    border-top: none;
  }

  button {
    width: 5rem;
  }
}

.chat-send {
  float: right;
  background-color: #ddd;
  margin: 1rem 0;

}

.chat-receive {
  float: left;
  background-color: #ddd;

}</style>