<script setup>
import { ref, reactive, onMounted } from 'vue';

let messages = ref(['yo', 'sup']);
let newMessage = ref('');
let socketServer = null;

onMounted(() => {
    socketServer = new WebSocket('wss://localhost:3000/primus');
    
    //listen for messages
    socketServer.onmessage = function (event) {
        let data = JSON.parse(event.data);
        if (data.action === 'newMessage') {
            messages.value.push(data.message);
        }
    };
});

const sendMessage = () => {
    let m = {
        "action": "newMessage",
        "message": newMessage.value
    };

    socketServer.send(JSON.stringify(m));
};
</script>

<template>
    <div>
        <h1>Chat</h1>
        <ul>
            <li v-for="m in messages">{{ m }}</li>
        </ul>
        <div><input type="text" v-model="newMessage"></div>
        <button @click="sendMessage">Send</button>
    </div>
</template>

<style scoped></style>