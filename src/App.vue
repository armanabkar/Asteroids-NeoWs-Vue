<template>
  <h2>Wednesday 19-Jan, there will be 4 near misses</h2>
  <hr />

  <div>
    <h2>2020 AD3</h2>
    <h3>Potentially hazardous? <span class="hazard">YES</span></h3>
    <p>
      Misses Earth tomorrow at 3:06 a.m. by 33,279,090 miles whilst travelling
      at 28,729 mph
    </p>
    <a href="">Find out more</a>
    <hr />
  </div>
  <div>
    <h2>2021 WV4</h2>
    <h3>Potentially hazardous? nope</h3>
    <p>
      Misses Earth tomorrow at 6:05 p.m. by 43,312,887 miles whilst travelling
      at 20,307 mph
    </p>
    <a href="">Find out more</a>
    <hr />
  </div>
  <div>
    <h2>2021 WV4</h2>
    <h3>Potentially hazardous? <span class="hazard">YES</span></h3>
    <p>
      Misses Earth tomorrow at 6:05 p.m. by 43,312,887 miles whilst travelling
      at 20,307 mph
    </p>
    <a href="">Find out more</a>
    <hr />
  </div>

  <!-- <h3>
      Getting data from NASA right now to check whether something from space is
      going to hit us. One moment…
    </h3> -->

  <footer>
    <p>
      This open-source project is powered by NASA Near Earth Object Web Service.
      You can get the source code from
      <a
        href="https://github.com/armanabkar/Asteroids-NeoWs-Vue"
        target="_blank"
        rel="noopener noreferrer"
        >here</a
      >.
    </p>
    <p>Designed & Developed by Arman Abkar</p>
  </footer>
</template>

<script>
export default {
  created() {
    document.title = "2 Potential Hazards ⚠️"
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
  background-image: url(./assets/stars.svg),
    linear-gradient(-60deg, #3d1b4b, var(--background));
  background-repeat: repeat, no-repeat;
  background-size: auto, cover;
  background-position: bottom, bottom;
  min-height: 100%;
}
body {
  position: relative;
  max-width: 40em;
  margin: 0 auto;
  padding: 20px;
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
  padding-top: 5px;
  font-size: 18px;
  font-weight: 500;
  text-align: center;
}
@media (max-width: 768px) {
  body {
    font-size: 18px;
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
