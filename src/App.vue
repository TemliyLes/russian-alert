<template>
  <ion-app>
    <div id="connection" v-if="!connectedFlag">
      <div class="loader_box">
        <div class="loader"></div>
        <p>Подключение</p>
      </div>

    </div>
    <ion-header>
      <ion-toolbar>
        <div class="bar">
          <img height="80px" src="https://upload.wikimedia.org/wikipedia/commons/5/57/Flag_of_Russian_Community.svg"
            alt="">
        </div>
      </ion-toolbar>
    </ion-header>
    <MapComponent @sos="sos" />
  </ion-app>
</template>

<script setup lang="ts">
import { IonApp } from '@ionic/vue';
import MapComponent from '@/components/MapComponent.vue';
import { ref, onMounted } from 'vue';

const URL = 'http://localhost:1313/';
const connection = ref();

const connectedFlag = ref(false)
const sos = () => {
  if (connection.value) {
    connection.value.send(JSON.stringify({
      type: 'register',
      body: window.location.href,
    }))
  } else {
    alert('no connection')
  }

}

onMounted(() => {
  setTimeout(() => {
    connection.value = new WebSocket(URL);
    connection.value.onmessage = (e: any) => {
      // console.log(e);
    }
    connection.value.onopen = (e: any) => {
      if (e.type == 'open') {
        connectedFlag.value = true
      }
      console.log(e);
    }
  }, 2000)

});
</script>


<style>
#connection {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 999999;
  width: 100%;
  height: 100dvh;
  background: #111;
  color: #ddd;
  display: grid;
  place-items: center;
}

.loader {
  width: 48px;
  height: 48px;
  border: 5px solid #FFF;
  border-bottom-color: transparent;
  border-radius: 50%;
  display: inline-block;
  box-sizing: border-box;
  animation: rotation 1s linear infinite;
}

.loader_box {
  height: 180px;
  display: flex;
  flex-direction: column;
  place-items: center;
}

@keyframes rotation {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}
</style>