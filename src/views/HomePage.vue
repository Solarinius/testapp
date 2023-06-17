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
    created: '',
    animationState: '', // Dynamic class that determines whether to fade advice in or out
    hourToFetchNewAdvice: null,
    lastSaveDate: null, // The last time we fetched new Advice
    today: new Date(),
  }),

  computed: {

  },

  // When we'll check if advice data needs to be changed
  async ionViewWillEnter() {
    this.fetchAdvice();
    setInterval(this.fetchAdvice, 60000);
  },



  methods: {
    async fetchAdvice() {

      let testdate = '2023-06-17T14:57';

      let time = new Date();
      time.setMinutes(time.getMinutes() - 1);
      time = new Date(time);

      let now = time.toISOString().split('.')[0].slice(0, -3);

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

          if (data["orders"].length > 0) {
            data["orders"].forEach(element => {
              this.advice = element['id'];
              this.created = element['date_created'];
            });
          }
          else {
            this.advice = 'nothing so far';
            this.created = now;
          }
        }).catch((error) => {
          this.advice = error;
          console.log('err = ' + error);


        });
    },
  },
});
</script>

<template>
  <IonPage>
    <div class="Home">

      <p>{{ advice }}</p>
      <p>{{ created }}</p>

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

p {
  background-color: blueviolet;
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