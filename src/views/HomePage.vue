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
    currentDate() {
      return this.today.getDate();
    },

    currentHour() {
      return this.today.getHours();
    },

    hourToEraseCurrentAdvice() {
      let oneHourPrior = this.hourToFetchNewAdvice - 1;
      if (oneHourPrior < 0) oneHourPrior = 23;
      return oneHourPrior;
    },
  },

  // When we'll check if advice data needs to be changed
  async ionViewWillEnter() {
    // If we haven't stored an hourToFetchNewAdvice before, calculate and store that and hourToEraseCurrentAdvice
    if (!this.hourToFetchNewAdvice) this.hourToFetchNewAdvice = this.currentHour

    this.updateAdvice()
  },

  methods: {
    async fetchAdvice() {

      const options = {
        'method': 'GET',
        'url': 'https://my.deal.by/api/v1/orders/list',
        'headers': {
          'Authorization': 'Bearer 09db5f5143554c6b2bc31920a1553be43e7f2c1a',
          "Content-Type": "application/json"
        }
      };

      /* const options = {
        'method': 'GET',
        'url': 'https://my.deal.by/api/v1/orders/list'
      }; */

      /* return await Http.request({
        method: 'GET',
        url: 'https://api.adviceslip.com/advice',

      }) */
      return await Http.request(
        options
      )
        .then(({ data }) => {
          // Set dynamic class to fade in text
          this.animationState = 'fadeIn'
          this.resetAnimationState()

          // Save new advice
          this.advice = data;
          // In other words:
          // const dataInJs = JSON.parse(data)
          // const slip = dataInJs.slip
          // this.advice = slip.advice
          // this.advice = this.advice.toUpperCase() — // font supports upper case only

          // Update lastSaveDate
          this.lastSaveDate = this.currentDate
        }).catch((error) => {
          this.advice = error;
        });
    },

    updateAdvice() {
      // If 24h have passed, fetch new advice
      if (this.currentHour === this.hourToFetchNewAdvice && this.currentDate != this.lastSaveDate) this.fetchAdvice()

      // If 23 hours have passed, start fading out current advice
      else if (this.currentHour === this.hourToEraseCurrentAdvice && this.advice) {
        // Set dynamic class to fade out text
        this.animationState = 'fadeOut'
        this.resetAnimationState()

        // Clear advice from state after the fade out animation ends
        setTimeout(() => {
          this.advice = ''
        }, 10000)
      }

      // Check every 10m if it's time to fetch/erase advice
      setTimeout(this.updateAdvice, 600000)
    },

    // Clear animation from advice after one playthrough
    // A safer approach might be to listen for `transistionend`
    resetAnimationState() {
      setTimeout(() => {
        this.animationState = ''
      }, 10000)
    }
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