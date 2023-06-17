<script lang="ts">
import { defineComponent } from 'vue';
import { IonPage } from '@ionic/vue';
import '@capacitor-community/http';
import { Plugins } from '@capacitor/core';
const { Http } = Plugins;

export default defineComponent({
  name: 'Home',
  components: { IonPage },

  data: () => ({
    advice: '', // Advice currently being displayed
    animationState: '', // Dynamic class that determines whether to fade advice in or out
    hourToFetchNewAdvice: null,
    lastSaveDate: null, // The last time we fetched new Advice
    today: new Date(),
  }),

  computed: {

  },

  // When we'll check if advice data needs to be changed
  async ionViewWillEnter() {
    setInterval(this.fetchAdvice, 60000);
  },



  methods: {
    async fetchAdvice() {

      let testdate = '2023-06-15T13:59:53';

      let now = new Date().toISOString().split('.')[0];

      console.log('type: ' + typeof (now));
      console.log('time: ' + now);

      let options = {
        'method': 'GET',
        'url': 'https://corsproxy.io/?https://my.deal.by/api/v1/orders/list',
        'headers': {
          'Authorization': 'Bearer 09db5f5143554c6b2bc31920a1553be43e7f2c1a',
          "Content-Type": "application/json"
        },
        params: { date_from: now },
      };

      return await Http.request(
        options
      )
        .then(({ data }) => {
          this.advice = data;
        }).catch((error) => {
          this.advice = error;
        });
    },
  },
});
</script>

<template>
  <IonPage>
    <div class="Home">

      <p class="embroidery" :class="animationState">{{ advice }}</p>

    </div>
  </IonPage>
</template>

<style scoped>
/* Center and style the content */
.Home {
  background: wheat;
  height: 100%;
  padding: 1rem;

  flex-direction: column;
  align-items: center;
  justify-content: space-between;

  /* Apply the custom font and style the text */
  font-size: 1rem;
  color: #002657;
  text-align: center;
}

/* Allow the flourishes to visually curve more closely around the text */
.embroidery {
  max-width: 686px;
  position: absolute;
  transform: translateY(-75%);
}

/* Animate new advice being added and old advice being removed */
.fadeIn {
  animation: fadeIn ease 3s;
}

.fadeOut {
  animation: fadeOut ease 3s;
}

@keyframes fadeIn {
  0% {
    opacity: 0;
  }

  100% {
    opacity: 1;
  }
}

@keyframes fadeOut {
  0% {
    opacity: 1;
  }

  100% {
    opacity: 0;
  }
}
</style>