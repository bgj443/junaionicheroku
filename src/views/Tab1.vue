<template>
  <ion-page>
    <ion-header>

      <ion-toolbar>
        <ion-title>Tab 1</ion-title>
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
      </ion-toolbar>


    
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Tab 1</ion-title>
        </ion-toolbar>
      </ion-header>
    
      <ExploreContainer name="Tab 1 page" />
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent } from '@ionic/vue';
import ExploreContainer from '@/components/ExploreContainer.vue';
// import getTrainsByStation from "../services/getTrainsByStation";
// import getStations from "../services/getStations";



export default  {

data() {
    return {
      departureStation: "",
      arrivalStation: "",
      trains: [],
      stations: ["stationShortCode", "stationName"],
      headers: [],
      trainCategories: ["Commuter", "Long-distance"]
    };
  },

//  methods: {
//     refreshTrains() {
//       if (this.departureStation) {
//         let departureStation = this.departureStation;
//         getTrainsByStation(this.departureStation).then(
//           (data) => (this.trains = data.sort(function compare(a, b) {
//             let indexA = a.timeTableRows.findIndex((el) => el.stationShortCode == departureStation);
//             let indexB = b.timeTableRows.findIndex((el) => el.stationShortCode == departureStation);
//             return new Date(a.timeTableRows[indexA].scheduledTime) - new Date(b.timeTableRows[indexB].scheduledTime);
//           }))
//         );
//       }
//     },
//     findDeparture(timetable) {
//       let departure = timetable.find(
//         (el) =>
//           el.stationShortCode == this.departureStation && el.type == "DEPARTURE"
//       );
//       return departure;
//     },
//     formatDate(value) {
//       let formatted = new Date(value).toLocaleDateString("fi-fi");
//       return formatted;
//     },
//     formatTime(value) {
//       let formatted = new Date(value).toLocaleTimeString("fi-fi", {
//         hour: "2-digit",
//         minute: "2-digit",
//       });
//       return formatted;
//     },
//     findStationName(stationShort) {
//       let stationName = this.stations.find(
//         (el) =>
//           el.stationShortCode == stationShort
//       );
//       return stationName.stationName.split(" ")[0];
//   },
//   },
//   mounted() {
//     getStations().then((data) => (this.stations = data));
//   },


  name: 'Tab1',
  components: { ExploreContainer, IonHeader, IonToolbar, IonTitle, IonContent, IonPage }
}
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