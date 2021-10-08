<template>
  <ion-page>
    <button>English</button>
      <Ionicbutton>Suomi</Ionicbutton>
    <ion-searchbar color="danger"></ion-searchbar>
    
    <ion-toolbar>
        <ion-title>Valitse Asema</ion-title>
      </ion-toolbar>
      <select v-model="departureStation" @change="refreshTrains">
      <option selected disabled></option>
      <template v-for="(station, i) in stations">
        <option
          v-bind:key="'station' + i"
          :value="station.stationShortCode"
          v-if="station.passengerTraffic === true"
        >
          {{ station.stationName }}
        </option>
      </template>
    </select>

      <ion-content class="ion-padding">
      <ion-button @click="toggleInfiniteScroll" expand="block">
        Toggle Infinite Scroll
      </ion-button>

      <ion-list>


        <ion-item v-for="item in items" :key="item">
          <ion-label>{{ item }}</ion-label>
        </ion-item>
      </ion-list>

      <ion-infinite-scroll
        @ionInfinite="loadData($event)" 
        threshold="100px" 
        id="infinite-scroll"
        :disabled="isDisabled"
      >
        <ion-infinite-scroll-content
          loading-spinner="bubbles"
          loading-text="Beep Boop, doing computer stuff">
        </ion-infinite-scroll-content>
      </ion-infinite-scroll>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { 
  IonButton,
  IonContent, 
  IonInfiniteScroll, 
  IonInfiniteScrollContent,
  IonItem,
  IonLabel,
  IonList,
  IonPage
 } from '@ionic/vue';
import { defineComponent, ref } from 'vue';
//import getTrainsByStation from "../services/getTrainsByStation";
//import getStations from "../services/getStations";



export default defineComponent({
  components: {
    IonButton,
    IonContent, 
    IonInfiniteScroll, 
    IonInfiniteScrollContent,
    IonItem,
    IonLabel,
    IonList,
    IonPage,
  },



  
  setup() {
    const isDisabled = ref(false);
    const toggleInfiniteScroll = () => {
      isDisabled.value = !isDisabled.value;
    }
    const items = ref([]);
   // const pushData = () => {
    //  const max = items.value.length + 20;
      //const min = max - 20;
      //for (let i = min; i < max; i++) {
        //items.value.push(i);
      //}
    }

    //const loadData = (ev: CustomEvent) => {
      //setTimeout(() => {
        //pushData();
        //console.log('Loaded data');
       // ev.target.complete();

        // App logic to determine if all data is loaded
        // and disable the infinite scroll
       // if (items.value.length == 1000) {
         // ev.target.disabled = true;
        //}
     // }, 500);
    //}

    //pushData();

    //return {
      //isDisabled,
      //toggleInfiniteScroll,
     // loadData,
     // items
    }
 // }
//}
);

</script>

<style>
.train-type {
  display: inline-block;
  border-radius: 50%;
  min-width: 30px;
  height: 30px;
  background: black;
  color: white;
  font-size: 16px;
  line-height: 30px;
  font-weight: bold;
  text-align: center;
  padding: 6px;
}
.train-destination {
  color: black;
  font-size: 16px;
  font-weight: bold;
  padding: 6px;
}
.train-track {
  color: black;
  font-size: 16px;
  font-weight: bold;
  padding: 6px;
}
.train-schedule {
  color: black;
  font-size: 16px;
  font-weight: bold;
  padding: 6px;
}
</style>