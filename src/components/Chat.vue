<template>
    <div>
        <div v-if="!connected" class="join-chat">
            <input type="text" v-model="username" placeholder="Username">
            <button @click="addUser">join to chat room</button>
        </div>
        <div v-if="connected">
            <div>
                <p> Connected to Chat Room as  <strong> {{ username }} </strong> </p>
                <section class="connected-users">
                    <div class="card-users">
                        <h2> Usuarios conectados: </h2>
                        <p v-for="(user,index) in listUsers" :key="index">
                            {{ user.username }}
                        </p>
                    </div>
                </section>
            </div>
            <div>
                <div class="window-chat">
                    <p v-for="(message,index) in messages" :key="index">
                        <strong>{{ message.username }}: </strong>
                        {{ message.message }}
                    </p>
                </div>
                <div class="input-chat">
                    <input type="text" v-model="message" @keypress="isUserTyping" placeholder="Escribe tu mensaje...">
                    <button @click="sendMessage">send</button>
                </div>
            </div>
            <div v-if="isTyping">
                <p> {{ userTyping }} is typing </p>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "Chat",
    data() {
        return{
            username: '',
            connected: false,
            listUsers: [],
            message: '',
            messages: [],
            isTyping: false,
            userTyping: ''
        }
    },
    methods:{
        addUser(){
            this.$socket.emit('add user', this.username);
        },
        sendMessage(){
            this.$socket.emit('newMessage', this.message);
            this.userStopTyping();
        },
        isUserTyping(){
            console.log("typing");
            console.dir(this.username);
            this.$socket.emit('typing', this.username);
        },
        userStopTyping(){
            this.$socket.emit('stopTyping', this.username);
        }
    },
    sockets:{
        login(data) {
            console.log(data);
            this.connected = data.status;
        },
        userJoined(data){
            console.log(data);
            this.listUsers = data.listUsers;
        },
        newMessage(data){
            this.messages.push(data);
        },
        typing(data){
            this.isTyping = true;
            this.userTyping = data.username;
        },
        stopTyping(data){
             this.isTyping = false;
             this.userTyping = data.username;
        }
    }
};
</script>

<style>
.connected-users {
    align-items: center;
    display: flex;
    justify-content: center;
    margin: 25px 0 45px;
}
.card-users {
    background: #17b517;
    max-width: 360px;
    padding: 10px 45px;
    border-radius: 5px;
    color: #fff;
}
button {
    background: #17b517;
    border: 0;
    padding: 10px 35px;
    color: #fff;
    border-radius: 3px;
    text-transform: capitalize;
}
input[type="text"] {
    padding: 8px;
    margin-right: 8px;
}
.join-chat{
    margin-top: 25px;
}
</style>