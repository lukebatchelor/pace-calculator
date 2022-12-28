<template>
  <v-app :theme="theme">
    <v-app-bar title="Pace Calculator">
      <v-spacer></v-spacer>

      <v-btn
        :prepend-icon="
          theme === 'light' ? 'mdi-weather-sunny' : 'mdi-weather-night'
        "
        @click="onClick"
      ></v-btn>
    </v-app-bar>

    <v-main v-if="activePage === 'from-pace'" class="px-4">
      <div class="text-h4 text-center mt-8 mb-8">
        Calculate total time from average pace
      </div>
      <v-container fill-height="100%" fluid>
        <v-row>
          <v-col>
            <v-text-field
              type="number"
              min="0"
              v-model.number="averagePaceMins"
              label="Average Pace"
              variant="outlined"
              suffix="mins"
              persistent-hint
            ></v-text-field>
          </v-col>
          <v-col>
            <v-text-field
              type="number"
              min="0"
              v-model.number="averagePaceSecs"
              variant="outlined"
              suffix="secs"
              persistent-hint
            ></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-text-field
              type="number"
              min="0"
              v-model.number="totalLength"
              label="Total Length"
              variant="outlined"
              suffix="km"
              persistent-hint
            ></v-text-field>
          </v-col>
        </v-row>
        <!-- <v-divider></v-divider> -->

        <v-row>
          <v-col>
            <div class="text-h6">Total Race time</div>
            <div class="text-h3 text-center">
              {{ calcRaceTime.calcTotalMins }}:{{ calcRaceTime.calcTotalSecs }}
            </div>
          </v-col>
        </v-row>
      </v-container>
    </v-main>

    <v-main v-if="activePage === 'from-goal-time'" class="px-4">
      <div class="text-h4 text-center mt-8 mb-8">
        Calculate average required pace from goal time
      </div>
      <v-container fill-height="100%" fluid>
        <v-row>
          <v-col>
            <v-text-field
              type="number"
              min="0"
              v-model.number="goalTimeMins"
              label="Goal time"
              variant="outlined"
              suffix="mins"
              persistent-hint
            ></v-text-field>
          </v-col>
          <v-col>
            <v-text-field
              type="number"
              min="0"
              v-model.number="goalTimeSecs"
              variant="outlined"
              suffix="secs"
              persistent-hint
            ></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-text-field
              type="number"
              min="0"
              v-model.number="totalLength"
              label="Total Length"
              variant="outlined"
              suffix="km"
              persistent-hint
            ></v-text-field>
          </v-col>
        </v-row>
        <!-- <v-divider></v-divider> -->

        <v-row>
          <v-col>
            <div class="text-h6">Required km pace</div>
            <div class="text-h3 text-center">
              {{ calcPace.reqMins }}:{{ calcPace.reqSecs }}/km
            </div>
          </v-col>
        </v-row>
      </v-container>
    </v-main>

    <v-bottom-navigation v-model="activePage" mandatory="force">
      <v-btn value="from-pace">
        <v-icon icon="mdi-timer"></v-icon>
        From Pace
      </v-btn>

      <v-btn value="from-goal-time">
        <v-icon icon="mdi-flag-checkered"></v-icon>
        From Goal Time
      </v-btn>
    </v-bottom-navigation>
  </v-app>
</template>

<style scoped></style>

<script setup lang="ts">
import { ref, watch } from 'vue';
import { computed } from '@vue/reactivity';

type Page = 'from-pace' | 'from-goal-time';

const theme = ref('dark');
const activePage = ref<Page>('from-pace');
const averagePaceMins = ref<number>(5);
const averagePaceSecs = ref<number>(50);
const goalTimeMins = ref<number>(50);
const goalTimeSecs = ref<number>(50);
const totalLength = ref<number>(10);

const calcRaceTime = computed(() => {
  const totalSecs =
    (averagePaceMins.value * 60 + averagePaceSecs.value) * totalLength.value;
  const calcTotalMins = Math.floor(totalSecs / 60);
  const calcTotalSecs = totalSecs - calcTotalMins * 60;
  return {
    calcTotalMins: String(calcTotalMins).padStart(2, '0'),
    calcTotalSecs: String(calcTotalSecs).padStart(2, '0'),
  };
});
const calcPace = computed(() => {
  const totalSecs = goalTimeMins.value * 60 + goalTimeSecs.value;
  const secsPerKm = totalSecs / totalLength.value;
  const reqMins = Math.floor(secsPerKm / 60);
  const reqSecs = Math.round(secsPerKm - reqMins * 60);
  console.log({ totalSecs, secsPerKm, reqMins, reqSecs });
  return { reqMins: reqMins, reqSecs: String(reqSecs).padStart(2, '0') };
});

function onClick() {
  theme.value = theme.value === 'light' ? 'dark' : 'light';
}
</script>
