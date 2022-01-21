<template>
  <Loading v-if="isLoading" />

  <Layout v-else data="data">
    <div v-for="asteroid in data" :key="asteroid.id">
      <h2>
        {{ asteroid.name }}
      </h2>
      <p>
        Potentially Hazardous?
        <strong>
          <span class="hazard" v-if="asteroid.is_potentially_hazardous_asteroid"
            >YES ⚠️</span
          ><span v-else>No</span></strong
        >
      </p>
      <p>
        Relative Velocity:
        <strong>
          {{ relativeVelocity(asteroid) }}
          mph</strong
        >
      </p>
      <p>
        Absolute Magnitude:
        <strong>{{ asteroid.absolute_magnitude_h }}</strong>
      </p>
      <p>
        Est. Diameter:
        <strong
          >{{ minEstimatedDiameter(asteroid) }}
          -
          {{ maxEstimatedDiameter(asteroid) }}
          ft</strong
        >
      </p>
      <p>
        Misses Earth at
        <strong>{{ approachDate(asteroid) }}</strong>
        by
        <strong>{{ missDistance(asteroid) }}</strong>
        miles
      </p>
      <p class="links">
        <a
          :href="asteroid.nasa_jpl_url"
          target="_blank"
          rel="noopener noreferrer"
          >Find Out More</a
        >
        <span class="id">ID: {{ asteroid.neo_reference_id }}</span>
      </p>
      <hr />
    </div>
  </Layout>
</template>

<script>
import { onMounted, ref } from "vue"
import formatNumber from "format-number"
import { format } from "date-fns"
import Loading from "./components/Loading.vue"
import Layout from "./components/Layout.vue"

export default {
  components: {
    Loading,
    Layout,
  },
  setup() {
    let data = ref([])
    let isLoading = ref(true)

    onMounted(() => {
      fetchData()
    })

    const relativeVelocity = (asteroid) =>
      formatNumber({ truncate: 0 })(
        asteroid.close_approach_data[0].relative_velocity.miles_per_hour
      )

    const minEstimatedDiameter = (asteroid) =>
      formatNumber({ truncate: 1 })(
        asteroid.estimated_diameter.feet.estimated_diameter_min
      )

    const maxEstimatedDiameter = (asteroid) =>
      formatNumber({ truncate: 1 })(
        asteroid.estimated_diameter.feet.estimated_diameter_max
      )

    const approachDate = (asteroid) =>
      format(
        asteroid.close_approach_data[0].epoch_date_close_approach,
        "EE d-MMM h:mmaaaa"
      )

    const missDistance = (asteroid) => {
      const formatter = formatNumber()
      return formatter(
        parseInt(asteroid.close_approach_data[0].miss_distance.miles, 10)
      )
    }

    const fetchData = () => {
      fetch(
        `https://api.nasa.gov/neo/rest/v1/feed?end_date=${getDate()}&&api_key=FRWjok2uvAkO7TkgtZF1JfoeCdKIccFKiuxuZhtm`
      )
        .then((res) => res.json())
        .then((res) => {
          isLoading.value = false
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
          hazards &&
            (document.title = `${hazards} Potential Hazards ⚠️ - Asteroids NeoWs`)
          data.value = fetchedData
        })
    }

    const getDate = () => {
      return `${new Date().toISOString().substr(0, 10)}`
    }

    return {
      data,
      isLoading,
      missDistance,
      approachDate,
      relativeVelocity,
      minEstimatedDiameter,
      maxEstimatedDiameter,
    }
  },
}
</script>

<style>
:root {
  --primary: #dfdfdf;
  --secondary: #b077c8;
  --background: #270237;
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
  max-width: 40em;
  margin: 0 auto;
  padding: 12px 20px 0 20px;
  font-family: Ubuntu Mono, Menlo, Consolas, Monaco, Liberation Mono,
    Lucida Console, monospace;
  font-size: 20px;
  color: var(--primary);
}
.id {
  color: var(--secondary);
}
.links {
  display: flex;
  justify-content: space-between;
}
a {
  color: var(--link);
}
.hazard {
  color: var(--red);
  font-weight: 700;
  -webkit-animation: hazard 1s infinite;
  animation: hazard 1s infinite;
  padding: 2px 4px;
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
@keyframes hazard {
  0% {
    color: var(--red);
    background: inherit;
  }

  50% {
    color: inherit;
    background: var(--red);
  }

  51% {
    color: var(--red);
    background: inherit;
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
