<template>
  <h3 v-if="isLoading" class="loading">
    Getting data from NASA right now to check whether something from space is
    going to hit us. One moment…
  </h3>

  <div v-else>
    <h2>
      {{ title }}
    </h2>
    <hr />

    <div v-for="astroid in data" :key="astroid.id">
      <h2>{{ astroid.name }}</h2>
      <p>
        Potentially hazardous?
        <strong>
          <span class="hazard" v-if="astroid.is_potentially_hazardous_asteroid"
            >YES</span
          ><span v-else>nope</span></strong
        >
      </p>
      <p>
        Relative Velocity:
        <strong>
          {{
            formatNumber({ truncate: 0 })(
              astroid.close_approach_data[0].relative_velocity.miles_per_hour
            )
          }}
          mph</strong
        >
      </p>
      <p>
        Misses Earth at
        <strong>{{
          format(
            astroid.close_approach_data[0].epoch_date_close_approach,
            "EE d-MMM h:mmaaaa"
          )
        }}</strong>
        by
        <strong>{{
          formatter(
            parseInt(astroid.close_approach_data[0].miss_distance.miles, 10)
          )
        }}</strong>
        miles
      </p>
      <p>
        <a
          :href="astroid.nasa_jpl_url"
          target="_blank"
          rel="noopener noreferrer"
          >Find out more</a
        >
      </p>
      <hr />
    </div>

    <footer>
      <p>
        This open-source project is powered by NASA Asteroids, Near Earth Object
        Web Service (NeoWS). You can get the source code from
        <a
          href="https://github.com/armanabkar/Asteroids-NeoWs-Vue"
          target="_blank"
          rel="noopener noreferrer"
          >here</a
        >.
      </p>
      <p>{{ footerName }}</p>
    </footer>
  </div>
</template>

<script>
import { onMounted, ref, computed } from "vue"
import formatNumber from "format-number"
import { format, addDays } from "date-fns"

export default {
  setup() {
    let data = ref([])
    let isLoading = ref(true)
    const formatter = formatNumber()

    onMounted(() => {
      fetchData()
    })

    const title = computed(
      () => `${format(new Date(), "EEEE d-MMM")}, there will be
      ${data.value.length} near misses`
    )

    const footerName = computed(
      () => `© ${new Date().getFullYear()} Arman Abkar`
    )

    const fetchData = () => {
      fetch(
        `https://api.nasa.gov/neo/rest/v1/feed?end_date=${getDate()}&&api_key=FRWjok2uvAkO7TkgtZF1JfoeCdKIccFKiuxuZhtm`
      )
        .then((res) => res.json())
        .then((res) => {
          isLoading.value = false
          let fetchedData = res.near_earth_objects[getDate()].sort(
            (a, b) =>
              a.close_approach_data[0].epoch_date_close_approach -
              b.close_approach_data[0].epoch_date_close_approach
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
      return `${addDays(new Date(), 1).toISOString().substr(0, 10)}`
    }

    return {
      data,
      isLoading,
      formatNumber,
      format,
      formatter,
      title,
      footerName,
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
  padding: 10px 20px 0 20px;
  font-family: Ubuntu Mono, Menlo, Consolas, Monaco, Liberation Mono,
    Lucida Console, monospace;
  font-size: 20px;
  color: var(--primary);
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
footer {
  color: var(--secondary);
  font-weight: 500;
  text-align: center;
}
.loading {
  text-align: center;
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
