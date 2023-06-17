<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Demo Application</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="small">Demo Application</ion-title>
        </ion-toolbar>
      </ion-header>

      <div id="container" v-bind:class="{ 'top-margin': !users, 'usersShowing': users }">

        <ion-button v-show="!users" @click="loadUsers()" expand="block">View All Users</ion-button>
        <strong v-show="users"> All Users</strong><br>
        <!-- <ion-list>
          <ion-item v-for="user in users" v-bind:key="user.id">
            <ion-label>{{ user.name }} </ion-label>
          </ion-item>
        </ion-list> -->
        <ion-label>{{ users }} </ion-label>
        <ion-button v-show="users" @click="users = null" color="danger">Hide Users</ion-button>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonList, IonItem, IonButton, IonLabel } from '@ionic/vue';
import { defineComponent } from 'vue';
import { CapacitorHttp } from '@capacitor/core';
import { Http, HttpResponse } from '@capacitor-community/http';

export default defineComponent({
  name: 'Home',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonLabel,
    IonButton,
    IonList,
    IonItem
  },
  data() {
    return { users: '' } // sets users to null on instantiation
  },
  methods: {
    async loadUsers() {

      const options = {
        url: 'https://my.deal.by/api/v1/orders/list',
        headers: { 'Authorization': 'Bearer 09db5f5143554c6b2bc31920a1553be43e7f2c1a' },
      };


      

      try {
        const response: HttpResponse = await CapacitorHttp.get(options);
        this.users = response.data;
      } catch (e) {
        this.users = e as string;
      }

    }
  },
}
)
</script>

<style scoped>
#container {
  text-align: center;
  position: absolute;
  left: 0;
  right: 0;

  transform: translateY(-50%);
}

.top-margin {
  top: 20%;
}

.usersShowing {
  margin-top: 70%;
}
</style>