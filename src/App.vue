<template>
  <div class="appbar">
    <img
      alt="Vue logo"
      class="logo"
      src="./assets/milli-piyango-logo.png"
      width="60px" />
    <div class="title">Moneymaker's Paradise!!</div>
  </div>
  <main>
    <div style="display: flex; align-items: center; gap: 16px; margin-right: 280px">
      <div
        class="ball"
        v-for="(number, index) in magicNumbers"
        :key="index">
        {{ number }}
      </div>
      <div
        @click="generateNumbers()"
        class="generate-button">
        Generate
      </div>
    </div>
    <div style="display: flex; align-items: center; gap: 64px">
      <div class="container">
        <img
          alt="Vue logo"
          class="logo"
          src="./assets/playslip_2x.jpg"
          width="1191px" />
        <div class="main-grid">
          <div
            class="sub-grid"
            v-for="(section, sectionIndex) in slip"
            :key="sectionIndex">
            <template
              v-for="(element, elementIndex) in section"
              :key="elementIndex">
              <div
                v-if="elementIndex + 1 <= 69"
                class="box"
                :style="{ backgroundColor: element.checked ? '#00ff0075' : 'unset' }"
                @click="toggle(element, section)"></div>
              <div
                v-else-if="elementIndex + 1 > 69 && elementIndex + 1 < 77"
                class="box"></div>
              <div
                v-else-if="elementIndex + 1 === 77"
                :class="[elementIndex + 1 === 77 ? 'yellow' : '']"
                class="box"
                @click="setRandom(sectionIndex)"></div>
            </template>
          </div>
        </div>
      </div>
      <div class="container">
        <img
          alt="Vue logo"
          class="logo"
          src="./assets/slip.jpg"
          width="300px" />

        <div class="coupon">
          <div
            class="coupon-row"
            v-for="(section, secIndex) in getNumbers"
            :key="secIndex">
            <div
              v-for="(number, numIndex) in section"
              :key="numIndex"
              :style="{ border: number.won ? '2px solid red' : 'unset' }"
              style="text-align: center; border-radius: 50%; width: 33px; height: 33px">
              {{ number.value.toString().padStart(2, '0') }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

const SECTION_COUNT = 5;
const ELEMENT_COUNT = 80;

type Slip = Section[];
type Section = Element[];
type Element = { checked: boolean; value: number; won: boolean };

const slip = ref<Slip>(bSlip());

const magicNumbers = ref<number[]>([0, 0, 0, 0, 0, 0]);

function bSlip() {
  const slip: Slip = [];

  for (let i = 1; i <= SECTION_COUNT; i++) {
    slip.push(bSection());
  }
  return slip;
}

function bSection(): Section {
  const section: Section = [];

  for (let i = 1; i <= ELEMENT_COUNT; i++) {
    section.push({ checked: false, value: i, won: false });
  }
  return section;
}

function toggle(element: Element, section: Section) {
  const checkedCount = section.filter((element) => element.checked).length;

  if (checkedCount < 6) element.checked = !element.checked;
  if (checkedCount === 6) element.checked = false;
}

function setRandom(sectionIndex: number) {
  const random = generateRandom();
  slip.value[sectionIndex].forEach((element) => {
    if (random.includes(element.value)) element.checked = true;
    else element.checked = false;
  });
}

function checkNumbers() {
  slip.value.forEach((section) => {
    section.forEach((number) => {
      if (magicNumbers.value.includes(number.value)) {
        number.won = true;
      } else {
        number.won = false;
      }
    });
  });
}

function generateNumbers() {
  magicNumbers.value = generateRandom();
  checkNumbers();
}

function generateRandom() {
  let numbers = [null, null, null, null, null, null];
  // console.log(numbers.map(() => Math.floor(Math.random() * 70)).sort((a, b) => a - b));
  return numbers.map(() => Math.floor(Math.random() * 70)).sort((a, b) => a - b);
}

// Computed..
const getNumbers = computed<Element[][]>(() => {
  const coupon = slip.value.map((section) => {
    return section
      .filter((element) => element.checked)
      .map((element) => {
        return element;
      });
  });
  return coupon;
});
</script>

<style scoped>
main {
  margin-top: 200px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 32px;
}

.title {
  display: flex;
  flex-direction: row;
  align-items: center;
  font-family: 'boldena';
  font-size: 45px;
}

.generate-button {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-left: 20px;
  font-size: 18px;
  border: 2px solid limegreen;
  width: 150px;
  height: 40px;
  color: limegreen;
}

.generate-button:hover {
  cursor: pointer;
  color: whitesmoke;
  border-color: whitesmoke;
}
.container {
  position: relative;
  z-index: 0;
}

.coupon {
  display: flex;
  flex-direction: column;
  gap: 15px;
  position: absolute;
  font-size: 20px;
  top: 105px;
  left: 38px;
  width: 260px;
  height: 210px;
  background-color: #d4ebf3;
}

.coupon-row {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: 6px;
  width: 225px;
  height: 11px;
}

.ball {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 60px;
  height: 60px;
  border: 2px solid limegreen;
  border-radius: 50%;
  color: limegreen;
  font-size: 22px;
}

.main-grid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  position: absolute;

  top: 36px;
  left: 194px;
}

.sub-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 5px;
  padding: 2px;
}

.appbar {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 22px;
  padding: 16px;
}

.box {
  width: 20px;
  height: 20px;
}

.box.yellow:hover {
  background: #ffff00b6;
}
</style>
