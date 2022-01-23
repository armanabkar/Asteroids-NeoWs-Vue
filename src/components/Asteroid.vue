<template>
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
    </strong>
  </p>
  <p>
    Absolute Magnitude:
    <strong>{{ asteroid.absolute_magnitude_h }}</strong>
  </p>
  <p>
    Est. Diameter:
    <strong>{{ estimatedDiameter(asteroid) }} </strong>
  </p>
  <p>
    Misses Earth at
    <strong>{{ approachDate(asteroid) }}</strong>
    by
    <strong>{{ missDistance(asteroid) }}</strong>
  </p>
  <p class="links">
    <a :href="asteroid.nasa_jpl_url" target="_blank" rel="noopener noreferrer"
      >Find Out More</a
    >
    <span class="id">ID: {{ asteroid.neo_reference_id }}</span>
  </p>
  <hr />
</template>

<script>
import formatNumber from "format-number"
import { format } from "date-fns"

export default {
  props: ["asteroid", "system"],
  setup({ asteroid, system }) {
    const relativeVelocity = (aster) =>
      system === "imperial"
        ? `${formatNumber({ truncate: 0 })(
            aster.close_approach_data[0].relative_velocity.miles_per_hour
          )} mph`
        : `${formatNumber({ truncate: 0 })(
            aster.close_approach_data[0].relative_velocity.kilometers_per_hour
          )} kph`

    const estimatedDiameter = (aster) =>
      system === "imperial"
        ? `${formatNumber({ truncate: 1 })(
            aster.estimated_diameter.feet.estimated_diameter_min
          )} - ${formatNumber({ truncate: 1 })(
            aster.estimated_diameter.feet.estimated_diameter_max
          )} ft`
        : `${formatNumber({ truncate: 1 })(
            aster.estimated_diameter.meters.estimated_diameter_min
          )} - ${formatNumber({ truncate: 1 })(
            aster.estimated_diameter.meters.estimated_diameter_max
          )} m`

    const approachDate = (aster) =>
      format(
        aster.close_approach_data[0].epoch_date_close_approach,
        "EE d-MMM h:mmaaaa"
      )

    const missDistance = (aster) => {
      const formatter = formatNumber()
      return system === "imperial"
        ? `${formatter(
            parseInt(aster.close_approach_data[0].miss_distance.miles, 10)
          )} miles`
        : `${formatter(
            parseInt(aster.close_approach_data[0].miss_distance.kilometers, 10)
          )} km`
    }

    return {
      missDistance,
      approachDate,
      estimatedDiameter,
      relativeVelocity,
      asteroid,
    }
  },
}
</script>

<style scoped>
.id {
  color: var(--secondary);
}
.links {
  display: flex;
  justify-content: space-between;
}
.hazard {
  color: var(--red);
  font-weight: 700;
  -webkit-animation: hazard 1s infinite;
  animation: hazard 1s infinite;
  padding: 2px 4px;
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
</style>