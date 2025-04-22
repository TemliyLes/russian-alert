<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <div class="bar">
          <img height="80px" src="https://upload.wikimedia.org/wikipedia/commons/5/57/Flag_of_Russian_Community.svg"
            alt="">
        </div>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <MapComponent />
        </ion-toolbar>
      </ion-header>

      <ExploreContainer name="Tab 1 page" />
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonPage, IonHeader, IonToolbar, IonContent } from '@ionic/vue';
import ExploreContainer from '@/components/ExploreContainer.vue';
import MapComponent from '@/components/MapComponent.vue';

</script>

<style>
.bar {
  background: #000;
  display: grid;
  place-items: center;
}
</style>
