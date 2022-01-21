<template>
  <h2>{{ title1 }}</h2>
  <h3>
    {{ title2 }}
  </h3>
  <hr />
  <slot></slot>

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
</template>

<script>
import { format } from "date-fns"
import { computed } from "vue"

export default {
  props: ["data"],
  setup(props) {
    const title1 = computed(() => `${format(new Date(), "EEEE d-MMM")},`)

    const title2 = computed(
      () => `There Will Be
      ${props.data.length} Near Misses`
    )

    const footerName = computed(
      () => `© ${new Date().getFullYear()} Arman Abkar`
    )

    return { title1, title2, footerName }
  },
}
</script>

<style scoped>
footer {
  color: var(--secondary);
  font-weight: 500;
  text-align: center;
}
</style>