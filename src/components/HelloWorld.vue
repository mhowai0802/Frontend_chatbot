<template>
  <div
    style="width: 100vw; height: 100vh; background-size: cover"
    v-if="darkTheme"
    :style="darkTheme && 'backgroundColor: #2C2D2E' && 'background-image:url(https://pica.zhimg.com/70/v2-d075b4ed7684f64cb4e81a0d20efe7e0_1440w.avis?source=172ae18b&biz_tag=Post)'"
    ></div>
    <div
    style="width: 100vw; height: 100vh; background-size: cover"
    v-if="!darkTheme"
    :style="!darkTheme && 'backgroundColor: #2C2D2E' && 'background-image:url(https://appmaster.io/cdn-cgi/image/width=1024,quality=83,format=auto/api/_files/ZC5TGYLhKnWGwvWUFtVyk7/download/)'"
    ></div>
  <div
    style="position: fixed; top: 0px; right: 0px; margin: 20px"
    :style="darkTheme && 'color: #fff'"
    @click="darkTheme = !darkTheme"
  >
    {{ darkTheme ? "Light" : "Dark" }} Style
  </div>

  <Chat
    :onSend="handleSendEvent"
    :chat="data"
    :bgColorHeader="darkTheme && '#1e1e1e'"
    :bgColorChat="darkTheme && '#2C2D2E'"
    :bgColorInput="darkTheme && '#1e1e1e'"
    :bgColorIcon="darkTheme && '#9B51E0'"
    :bgColorMessageChatbot="darkTheme && '#1e1e1e'"
    :bgColorMessagePerson="darkTheme && '#9B51E0'"
    :bgColorMessageTimestamp="darkTheme && '#1e1e1e'"
    :textColorInput="darkTheme && '#fff'"
    :textColorHeader="darkTheme && '#fff'"
    :textColorMessageChatbot="darkTheme && '#fff'"
    :textColorMessageTimestamp="darkTheme && '#fff'"
  />
</template>
<script setup>
import { ref } from "vue";
import { Chat } from "@chat-ui/vue3";
import axios from "axios";

const data = ref([{ message: 'You may ask today rainfall, movie commendation, and news', type: 'chatbot', timestamp: '3:45 PM' }]);
const darkTheme = ref(true);

function handleSendEvent(input) {
  if (input == "") return;
  const messagePerson = {
    type: "person",
    timestamp: formatAMPM(new Date()),
    message: input,
  };
  data.value.push(messagePerson);

  setTimeout(async () => {
    const json = JSON.stringify({
      question: data["_rawValue"][data["_rawValue"].length-1]["message"],
    });
    const response = await axios.post(
      "http://127.0.0.1:5001/api/chatbot",
      json,
      { headers: { "Content-Type": "application/json" } }
    );
    for (let res of response["data"]["message"]) {
      const messageChatbot = {
        type: "chatbot",
        timestamp: formatAMPM(new Date()),
        message: res,
      };
      data.value.push(messageChatbot);
    }
  }, getRandomNumber());
}

function getRandomNumber() {
  return Math.floor(Math.random() * (2000 - 1000 + 1)) + 1000;
}

function formatAMPM(date) {
  var hours = date.getHours();
  var minutes = date.getMinutes();
  var ampm = hours >= 12 ? "PM" : "AM";
  hours = hours % 12;
  hours = hours ? hours : 12; // the hour '0' should be '12'
  minutes = minutes < 10 ? "0" + minutes : minutes;
  var strTime = hours + ":" + minutes + " " + ampm;
  return strTime;
}
</script>
<style>
body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
