<script setup>
import { ref, onMounted } from 'vue';
import { API_KEY, BASE_URL } from './constants/index';
import WeatherSummary from './components/WeatherSummary.vue';
import Highlights from './components/Highlights.vue';
import Coords from './components/Coords.vue';
import Humidity from './components/Humidity.vue';

const city = ref('Khotkovo');
const weatherInfo = ref(null);

const loading = ref(true);

const getWeather = async () => {
  try {
    const response = await fetch(
      `${BASE_URL}?q=${city.value}&units=metric&appid=${API_KEY}`,
    );
    const data = await response.json();
    weatherInfo.value = data;
    loading.value = false;
  } catch (error) {
    console.error('Ошибка при получении данных о погоде:', error);
    loading.value = false;
  }
};
onMounted(getWeather);
</script>

<template>
  <div class="page">
    <main class="main">
      <div class="container">
        <div class="laptop">
          <div v-if="loading" class="loader"></div>
          <div v-else>
            <div class="sections">
              <section class="section section-left">
                <div class="info">
                  <div class="city-inner">
                    <input
                      v-model="city"
                      @keyup.enter="getWeather"
                      type="text"
                      class="search" />
                  </div>
                  <WeatherSummary :weatherInfo="weatherInfo" />
                </div>
              </section>
              <section class="section section-right">
                <Highlights :weatherInfo="weatherInfo" />
              </section>
            </div>
          </div>
          <div v-if="weatherInfo?.weather" class="sections">
            <Coords :coord="weatherInfo.coord" />
            <Humidity :humidity="weatherInfo.main.humidity" />
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<style lang="sass" scoped>
@import './assets/styles/main'
.page
  position: relative
  display: flex
  justify-content: center
  align-items: center
  min-height: 100vh
  padding: 20px 0
  background-color: #412345

.laptop
  width: 100%
  padding: 20px
  background-color: #2D1B2F
  border-radius: 25px
  border: 1px solid white

.sections
  display: flex
  width: 100%

  @media (max-width: 767px)
    flex-direction: column

.section-left
  width: 30%
  padding-right: 10px

  @media (max-width: 767px)
    width: 100%
    padding-right: 0

.section-right
  width: 70%
  padding-left: 10px

  @media (max-width: 767px)
    width: 100%
    margin-top: 16px
    padding-left: 0

.city-inner
  position: relative
  display: inline-block
  width: 100%

  &::after
    content: ''
    position: absolute
    top: 0
    right: 10px
    width: 25px
    height: 25px
    background: url('./assets/img/search.svg') no-repeat 50% 50%
    background-size: contain
    transform: translateY(50%)
    cursor: pointer

.info
  height: 100%
  padding: 16px
  background: url('./assets/img/gradient-1.jpg') no-repeat 50% 50%
  background-size: cover
  border-radius: 25px
  border: 0.5px solid white

.search
  width: 100%
  padding: 16px
  font-family: 'Inter', Arial, sans-serif
  color: $white
  background-color: rgba(0, 0, 0, 0.75)
  border-radius: 16px
  border: none
  outline: none
  cursor: pointer

.section-bottom
  width: 50%
  margin-top: 16px

  @media (max-width: 767px)
    width: 100%

</style>
