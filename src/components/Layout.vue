<template>
  <h2>{{ title1 }}</h2>
  <h3>
    {{ title2 }}
  </h3>
  <p class="buttons">
    <button @click="fetchData">Reload</button>
    <button @click="changeSystem">
      {{ system === "imperial" ? "to metric" : "to imperial" }}
    </button>
  </p>
  <hr />
  <slot></slot>
  <footer>
    <p class="buttons">
      <button @click="changeDay(-1)" v-if="plusDay">← Previous Day</button
      ><span v-else></span><button @click="changeDay(+1)">Next Day →</button>
    </p>
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
</template>

<script>
import { format, addDays } from "date-fns"
import { computed } from "vue"

export default {
  props: ["data", "fetchData", "system", "plusDay", "changeDay"],
  setup({ fetchData, data, system, plusDay, changeDay }) {
    const title1 = computed(
      () => `${format(addDays(new Date(), plusDay), "EEEE d-MMM")},`
    )

    const title2 = computed(
      () => `There Will Be
      ${data.length} Near Misses`
    )

    const footerName = computed(
      () => `© ${new Date().getFullYear()} Arman Abkar`
    )

    const changeSystem = () => {
      system === "imperial" ? fetchData("metric") : fetchData("imperial")
    }

    return {
      title1,
      title2,
      footerName,
      fetchData,
      system,
      changeSystem,
      changeDay,
    }
  },
}
</script>

<style scoped>
.buttons {
  display: flex;
  justify-content: space-between;
}
button {
  margin: 0;
  padding: 0;
  border: none;
  font-size: 1em;
  cursor: pointer;
  font-family: Ubuntu Mono, Menlo, Consolas, Monaco, Liberation Mono,
    Lucida Console, monospace;
  font-weight: 600;
  background-color: transparent;
  text-transform: capitalize;
  color: var(--link);
}
button:hover {
  color: var(--primary);
}
.active {
  color: var(--secondary);
}
footer {
  color: var(--secondary);
  font-weight: 500;
  text-align: center;
}
</style>