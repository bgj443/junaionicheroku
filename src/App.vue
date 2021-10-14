<template>
  <ion-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>Valitse lähtöasema</span>
      </v-toolbar-title>
    </v-toolbar>
    <ion-router-outlet />
    <v-content>
      <Trains />
    </v-content>
  </ion-app>
</template>

<script lang="js">
import { IonApp, IonRouterOutlet } from '@ionic/vue';
import { defineComponent } from 'vue';
import Trains from "./components/Trains";

export default defineComponent({
  name: 'App',
  components: {
    IonApp,
    IonRouterOutlet,
    Trains
  },
    data() {
    return {
      //
    };
  },
});
</script>