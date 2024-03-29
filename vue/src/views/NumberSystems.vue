<template>
  <div class="pb-1">
    <div class="h-2.5">
        <TimerBar v-if="trainRunning" :time="timeToSolve" :widthprops="'100%'"> </TimerBar>
    </div>
    

    <span
      v-if="trainRunning || trainingEnded"
      @click="leaveTrain"
      class="absolute pt-6 right-5"
    >
      <XMarkIcon
        class="block h-8 w-8 border border-gray-600 rounded-full p-1 text-gray-600 transition duration-300 ease-in-out hover:rounded-lg cursor-pointer"
      />
    </span>

    <div class="h-96 pt-10  mb-20">
      <div v-if="intro" class="h-96">
        <div class="py-2 text-center">
          <h1 class="text-5xl text-white">Prevody číselných sústav</h1>
        </div>
        <div class="py-6 text-center">
          <h1 class="text-xl text-white">
            Preveďte číslo do inej sustavy.
          </h1>
          <h1 class="text-xl text-white">
            Budeme merať koľko prevodov dokažete spraviť za 2 minúty.
          </h1>
          <button
              @click="startTrain"
              class="border rounded-full mt-20 px-2 py-2 bg-white font-bold text-gray-600 myButtonShadow hover:text-amber-600"
          >
              Kliknite tu a začnite.
          </button>
        </div>
      </div>

      <div v-if="trainRunning">
        <div class="flex pt-10 pb-0 justify-center">
          <NumSysInputComponent @update-score="updateScore">
          </NumSysInputComponent>
          <NumSysInputComponent @update-score="updateScore">
          </NumSysInputComponent>
          <NumSysInputComponent @update-score="updateScore">
          </NumSysInputComponent>
        </div>
        <p class="text-white text-2xl">Score: {{ score }}</p>
      </div>

      <div>
        <div v-if="trainingEnded" class="pt-10 w-96 mx-auto text-center">
          <div class="text-2xl text-white text-center">
            <p>Vaše skóre za 2 minúty je</p>
          </div>
        </div>

        <div v-if="trainingEnded" class="w-full h-80">
          <div class="text-4xl text-white text-center py-9">
            <p>{{ score }}</p>
            <p v-if="score == 1" class="text-2xl pt-6">prevod.</p>
            <p v-if="score > 1 && score <=4" class="text-2xl pt-6">prevody.</p>
            <p v-if="score == 0 || score > 4" class="text-2xl pt-6">prevodov.</p>
          </div>
          <div class="text-lg text-white text-center py-6">
            <button
                  @click="startTrain"
                  
                  class="inline-block bg-white mx-3 hover:text-amber-600 text-gray-600 myButtonShadow font-bold py-1 px-4 rounded-full"
                  >
                  Skúste to znova!
              </button>
              <button
                  @click="saveScore"
                  :disabled="scoreSaved"
                  class="inline-block bg-white mx-3 hover:text-amber-600 text-gray-600 myButtonShadow font-bold py-1 px-4 rounded-full"
                  >
                  <p v-if="!scoreSaved">Uložiť skóre</p>
                  <p v-if="scoreSaved">Skóre uložené</p>
              </button>
          </div>
        </div>
      </div>
    </div>
    <ActivityTrackerComponent 
      :startMeasurement="startMeasurement"
      :stopMeasurement="stopMeasurement"
      @time-spent="onTimeSpent" 
    />
  </div>
</template>

<script>
import { XMarkIcon } from "@heroicons/vue/24/outline";
import NumSysInputComponent from "../components/NumSysInputComponent.vue";
import ActivityTrackerComponent from "../components/ActivityTrackerComponent.vue"
import TimerBar from "../components/TimerBar.vue";
import { reactive, toRefs } from "vue";
import { ref, computed, watch, onMounted } from "vue";
import store from "../store"

export default {
  components: {
    NumSysInputComponent,
    XMarkIcon,
    TimerBar,
    ActivityTrackerComponent,
  },

  setup() {
    const state = reactive({
      trainingEnded: false,
      trainRunning: false,
      intro: true,
      scoreSaved: false,
      startMeasurement: false,
      stopMeasurement: false,
    });

    const data = reactive({
        score: 0,
        timeToSolve: 120,
    });

    const startTrain = () => {
      data.score = 0;
      state.trainRunning = true;
      state.intro = false;
      state.trainingEnded = false;
      state.scoreSaved = false;
      startGame();
      training();
    };

    const stopTrain = () => {
      state.trainRunning = false;
      endGame();
      state.intro = false;
      state.trainingEnded = true;
    };

    const leaveTrain = () => {
      state.trainRunning = false;
      endGame();
      state.intro = true;
      state.trainingEnded = false;
    };

    const training = () => {
      setTimeout(() => {
            stopTrain()
        }, data.timeToSolve * 1000);
    };

    const updateScore = () => {
        data.score += 1
    };

    const saveScore = () => {
      const score = {
          game_id: 6,
          score: data.score
      }

      store.dispatch('addScore', score);
      state.scoreSaved = true;
    }

    const startGame = () => {
        state.startMeasurement = true;
        state.stopMeasurement = false;
    };
    
    const endGame = () => {
        state.stopMeasurement = true;
        state.startMeasurement = false;
    };

    const onTimeSpent = (time) => {
        const activityData = {
            game_id: 6,
            training_time: time
        }
        store.dispatch("addSpentTime", activityData);
    };

    return {
      ...toRefs(state),
      ...toRefs(data),
      startTrain,
      leaveTrain,
      updateScore,
      saveScore,
      onTimeSpent,
    };
  },

  mounted() {
      window.scrollTo(0, 0);
  }
};
</script>