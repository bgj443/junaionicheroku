<template>
  <div>
    <ion-searchbar
      placeholder="Hae asemaa"
      show-cancel-button="always"
      @ionFocus="showStationList = true"
      @ionCancel="showStationList = false"
      @ionChange="updateSearch($event)"
    >
    </ion-searchbar>
    <ion-list v-if="showStationList == true">
      <template v-for="(station, i) in stations">
        <ion-item
          button
          @click="
            departureStation = station.stationShortCode;
            refreshTrains();
            showStationList = false;
          "
          v-bind:key="'station' + i"
          :value="station.stationShortCode"
          v-if="station.passengerTraffic === true"
        >
          {{ station.stationName }}
        </ion-item>
      </template>
    </ion-list>
    <ion-segment value="departures" @ionChange="showTable($event)">
      <ion-segment-button value="departures">
        <ion-label>Lähtevät</ion-label>
      </ion-segment-button>
      <ion-segment-button value="arrivals">
        <ion-label>Saapuvat</ion-label>
      </ion-segment-button>
    </ion-segment>
    <span class="train-category-filter">
      <ion-chip @click="updateFilter('Commuter')">
        <input
          type="checkbox"
          id="commuter"
          value="Commuter"
          v-model="trainCategories"
          @change="refreshTrains"
        />
        <ion-label>Lähijunat</ion-label>
      </ion-chip>
      <ion-chip @click="updateFilter('Long-distance')">
        <input
          type="checkbox"
          id="long-distance"
          value="Long-distance"
          v-model="trainCategories"
          @change="refreshTrains"
        />
        <ion-label>Kaukojunat</ion-label>
      </ion-chip>
    </span>
    <template v-if="trains.length">
      <!--Lähtevät-->
      <div v-if="showTables.includes('departures')">
        <div
          class="departure-table"
          v-for="(train, i) in trains"
          v-bind:key="i"
        >
          <template
            v-if="
              findDeparture(train.timeTableRows) &&
                trainCategories.includes(train.trainCategory) &&
                findDeparture(train.timeTableRows).scheduledTime >= 
                (new Date().toISOString())
            "
          >
            <router-link
              :to="
                '/tabs/tab3/' + train.departureDate + '/' + train.trainNumber
              "
            >
              <span class="train-type">{{
                train.commuterLineID
                  ? train.commuterLineID
                  : train.trainType + train.trainNumber
              }}</span>
              <span class="train-destination" v-if="train.timeTableRows.length">
                {{
                  findStationName(
                    train.timeTableRows[train.timeTableRows.length - 1]
                      .stationShortCode
                  )
                }}
              </span>
              <span class="train-track">
                {{ findDeparture(train.timeTableRows).commercialTrack }}
              </span>
              <span class="train-schedule">
                {{
                  formatDate(findDeparture(train.timeTableRows).scheduledTime)
                }}
              </span>
              <span class="train-schedule" v-if="train.cancelled == false">
                {{
                  formatTime(findDeparture(train.timeTableRows).scheduledTime)
                }}
              </span>
              <span class="train-live-schedule" v-else>
                {{ "Peruttu" }}
              </span>
              <span
                class="train-live-schedule"
                v-if="
                  findDeparture(train.timeTableRows).liveEstimateTime &&
                    formatTime(
                      findDeparture(train.timeTableRows).liveEstimateTime
                    ) !=
                      formatTime(
                        findDeparture(train.timeTableRows).scheduledTime
                      )
                "
              >
                {{
                  "~" +
                    formatTime(
                      findDeparture(train.timeTableRows).liveEstimateTime
                    )
                }}
              </span>
            </router-link>
          </template>
        </div>
      </div>
      <!--Saapuvat-->
      <div v-if="showTables.includes('arrivals')">
        <div class="arrival-table" v-for="(train, i) in trains" v-bind:key="i">
          <template
            v-if="
              findArrival(train.timeTableRows) &&
                trainCategories.includes(train.trainCategory)
            "
          >
            <router-link
              :to="
                '/tabs/tab3/' + train.departureDate + '/' + train.trainNumber
              "
            >
              <span class="train-type">{{
                train.commuterLineID
                  ? train.commuterLineID
                  : train.trainType + train.trainNumber
              }}</span>
              <span class="train-destination" v-if="train.timeTableRows.length">
                {{
                  findStationName(
                    train.timeTableRows[train.timeTableRows.length - 1]
                      .stationShortCode
                  )
                }}
              </span>
              <span class="train-track">
                {{ findArrival(train.timeTableRows).commercialTrack }}
              </span>
              <span class="train-schedule">
                {{ formatDate(findArrival(train.timeTableRows).scheduledTime) }}
              </span>
              <span class="train-schedule" v-if="train.cancelled == false">
                {{ formatTime(findArrival(train.timeTableRows).scheduledTime) }}
              </span>
              <span class="train-live-schedule" v-else>
                {{ "Peruttu" }}
              </span>
              <span
                class="train-live-schedule"
                v-if="
                  findArrival(train.timeTableRows).liveEstimateTime &&
                    formatTime(
                      findArrival(train.timeTableRows).liveEstimateTime
                    ) !=
                      formatTime(findArrival(train.timeTableRows).scheduledTime)
                "
              >
                {{
                  "~" +
                    formatTime(
                      findArrival(train.timeTableRows).liveEstimateTime
                    )
                }}
              </span>
            </router-link>
          </template>
        </div>
      </div>
    </template>
  </div>
</template>

<script>
import getTrainsByStation from "../services/getTrainsByStation";
import getStations from "../services/getStations";
import {
  IonSegment,
  IonLabel,
  IonSegmentButton,
  IonSearchbar,
  IonList,
  IonItem,
  IonChip
} from "@ionic/vue";
export default {
  components: {
    IonSegment,
    IonLabel,
    IonSegmentButton,
    IonSearchbar,
    IonList,
    IonItem,
    IonChip
  },
  data() {
    return {
      departureStation: "",
      arrivalStation: "",
      trains: [],
      stations: ["stationShortCode", "stationName"],
      headers: [],
      trainCategories: ["Commuter", "Long-distance"],
      showTables: ["departures"],
      showStationList: false,
    };
  },
  methods: {
    refreshTrains() {
      if (this.departureStation) {
        let departureStation = this.departureStation;
        getTrainsByStation(this.departureStation).then(
          (data) =>
            (this.trains = data.sort(function compare(a, b) {
              let indexA = a.timeTableRows.findIndex(
                (el) => el.stationShortCode == departureStation
              );
              let indexB = b.timeTableRows.findIndex(
                (el) => el.stationShortCode == departureStation
              );
              return (
                new Date(a.timeTableRows[indexA].scheduledTime) -
                new Date(b.timeTableRows[indexB].scheduledTime)
              );
            }))
        );
      }
    },
    findDeparture(timetable) {
      let departure = timetable.find(
        (el) =>
          el.stationShortCode == this.departureStation && el.type == "DEPARTURE"
      );
      return departure;
    },
    findArrival(timetable) {
      let arrival = timetable.find(
        (el) =>
          el.stationShortCode == this.departureStation && el.type == "ARRIVAL"
      );
      return arrival;
    },
    formatDate(value) {
      let formatted = new Date(value).toLocaleDateString("fi-fi");
      return formatted;
    },
    formatTime(value) {
      let formatted = new Date(value).toLocaleTimeString("fi-fi", {
        hour: "2-digit",
        minute: "2-digit",
      });
      return formatted;
    },
    findStationName(stationShort) {
      let stationName = this.stations.find(
        (el) => el.stationShortCode == stationShort
      );
      return stationName.stationName.split(" ")[0];
    },
    showTable(event) {
      event.detail.value == "arrivals"
        ? (this.showTables = ["arrivals"])
        : (this.showTables = ["departures"]);
    },
    updateSearch(event) {
      let items = Array.from(document.querySelector("ion-list").children);
      let query = event.target.value.toLowerCase();
      requestAnimationFrame(() => {
        items.forEach((item) => {
          let shouldShow = item.textContent.toLowerCase().indexOf(query) > -1;
          item.style.display = shouldShow ? "block" : "none";
        });
      });
    },
    updateFilter(value) {
      this.trainCategories.includes(value)
      ? this.trainCategories = this.trainCategories.filter(e => e !== value)
      : this.trainCategories.push(value)
    }
  },
  mounted() {
    getStations().then((data) => (this.stations = data));
  },
};
</script>

<style>
a {
  text-decoration: none;
}
.train-type {
  display: inline-block;
  border-radius: 50%;
  min-width: 30px;
  height: 30px;
  background: var(--ion-color-dark);
  color: var(--ion-color-light);
  font-size: 16px;
  line-height: 30px;
  text-align: center;
  padding: 6px;
}
.train-destination {
  color: var(--ion-color-dark);
  font-size: 16px;
  font-weight: bold;
  padding: 6px;
}
.train-track {
  color: var(--ion-color-dark);
  font-size: 16px;
  font-weight: bold;
  padding: 6px;
}
.train-schedule {
  color: var(--ion-color-dark);
  font-size: 16px;
  font-weight: bold;
  padding: 6px;
}
.train-live-schedule {
  color: var(--ion-color-danger);
  font-size: 16px;
  font-weight: bold;
  padding: 6px;
}
ion-chip > ion-label {
  padding: 5px;
}
</style>
