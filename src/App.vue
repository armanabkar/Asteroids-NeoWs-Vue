<template>
  <Loading v-if="isLoading" />

  <Layout
    v-else
    :data="data"
    :fetchData="fetchData"
    :system="system"
    :plusDay="plusDay"
    :changeDay="changeDay"
  >
    <div v-for="asteroid in data" :key="asteroid.id">
      <Asteroid :asteroid="asteroid" :system="system" />
    </div>
  </Layout>
</template>

<script>
import { onMounted, ref } from "vue"
import { addDays } from "date-fns"
import Loading from "./components/Loading.vue"
import Layout from "./components/Layout.vue"
import Asteroid from "./components/Asteroid.vue"

export default {
  components: {
    Loading,
    Layout,
    Asteroid,
  },
  setup() {
    let data = ref([])
    let isLoading = ref(false)
    let system = ref("imperial")
    let plusDay = ref(0)

    onMounted(() => {
      fetchData(system.value)
    })

    const fetchData = (type) => {
      isLoading.value = true
      system.value = type
      fetch(
        `https://api.nasa.gov/neo/rest/v1/feed?end_date=${getDate()}&&api_key=DEMO_KEY`
      )
        .then((res) => res.json())
        .then((res) => {
          let fetchedData = res.near_earth_objects[getDate()]
            .sort(
              (a, b) =>
                a.close_approach_data[0].epoch_date_close_approach -
                b.close_approach_data[0].epoch_date_close_approach
            )
            .filter(
              (asteroid) =>
                asteroid.close_approach_data[0].epoch_date_close_approach >
                new Date()
            )

          const hazards = fetchedData.reduce((acc, curr) => {
            if (curr.is_potentially_hazardous_asteroid) {
              return acc + 1
            }
            return acc
          }, 0)
          hazards
            ? (document.title = `${hazards} Potential Hazards ⚠️ - Near Earth Asteroids`)
            : (document.title = "Near Earth Asteroids")

          data.value = fetchedData
          isLoading.value = false
        })
    }

    const getDate = () => {
      return `${addDays(new Date(), plusDay.value).toISOString().substr(0, 10)}`
    }

    const changeDay = (day) => {
      plusDay.value += day
      fetchData(system.value)
    }

    return {
      data,
      isLoading,
      fetchData,
      system,
      plusDay,
      changeDay,
    }
  },
}
</script>

<style>
:root {
  --primary: #dfdfdf;
  --secondary: #7d7a98;
  --background: #262254;
  --link: #f47d20;
  --red: red;
}
html {
  background-image: url(./assets/stars.svg);
  background-repeat: repeat, no-repeat;
  background-size: auto, cover;
  background-position: bottom, bottom;
  min-height: 100%;
  background-color: var(--background);
}
body {
  position: relative;
  max-width: 42em;
  margin: 0 auto;
  padding: 12px 20px 0 20px;
  font-family: Ubuntu Mono, Menlo, Consolas, Monaco, Liberation Mono,
    Lucida Console, monospace;
  font-size: 20px;
  color: var(--primary);
  scrollbar-width: thin;
  scrollbar-color: var(--primary) var(--background);
}
a {
  color: var(--link);
}
hr {
  border: 0;
  border-bottom: 4px solid var(--secondary);
}
@media (max-width: 600px) {
  body {
    font-size: 16px;
  }
}
::-moz-selection {
  color: var(--background);
  background: var(--primary);
}
::selection {
  color: var(--background);
  background: var(--primary);
}
::-webkit-scrollbar {
  width: 10px;
  overflow: auto;
}
::-webkit-scrollbar-track {
  background: var(--background);
}
::-webkit-scrollbar-thumb {
  background-color: var(--primary);
}
</style>
