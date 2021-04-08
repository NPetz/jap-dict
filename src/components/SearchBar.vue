


<template>
  <form
    id="form"
    v-bind:class="{ firstVisit: firstTime, shake: shake }"
    v-on:submit.prevent="
      onSubmit($event);
      $emit('search', $event.target[0].value);
    "
  >
    <input type="text" placeholder="lookup a japanese word" id="input" />
    <button class="ripple" type="submit">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        xmlns:xlink="http://www.w3.org/1999/xlink"
        version="1.1"
        width="20px"
        height="20px"
        viewBox="0 0 124.524 124.524"
        xml:space="preserve"
      >
        <path
          fill="#eee"
          d="M51,102.05c10.5,0,20.2-3.2,28.3-8.6l29.3,29.3c2.301,2.3,6.101,2.3,8.5,0l5.7-5.7c2.3-2.3,2.3-6.1,0-8.5L93.4,79.35   c5.399-8.1,8.6-17.8,8.6-28.3c0-28.1-22.9-51-51-51c-28.1,0-51,22.9-51,51C0,79.149,22.8,102.05,51,102.05z M51,20.05   c17.1,0,31,13.9,31,31c0,17.1-13.9,31-31,31c-17.1,0-31-13.9-31-31C20,33.95,33.9,20.05,51,20.05z"
        />
      </svg>
    </button>
  </form>
</template>

<script>
export default {
  name: "SearchBar",
  data: () => ({ firstTime: true, shake: false }),
  methods: {
    onSubmit(e) {
      let val = e.target[0].value;
      if (val.length > 0) {
        this.firstTime = false;
        // remove
        // TODO this should be in the main app
        // let hits = document.querySelectorAll(".active");
        // hits.forEach((x) => {
        //   x.classList.remove("active");
        // });
      } else {
        this.shake = true;
        setTimeout(() => (this.shake = false), 820);
      }
    },
  },
};
</script>

<style scoped>
form {
  display: flex;
  font-family: "Montserrat", "Noto Serif JP";
  font-size: 1rem;
  transition: all 1s ease;
}
.firstVisit {
  margin-top: calc(50vh - 3rem);
}
.shake {
  animation: shake 0.82s cubic-bezier(0.36, 0.07, 0.19, 0.97) both;
}

input {
  text-align: center;
  flex: 3 1 auto;
  padding: 1em;
  border: none;
  font-family: "Montserrat", "Noto Serif JP";
  font-size: 1em;
  min-width: 0;
  border: 1px solid #333;
  border-right: none;
}
button {
  flex: 1 0 auto;
  white-space: nowrap;
  padding: 1rem;
  border: none;
  font-family: "Montserrat", "Noto Serif JP";
  font-weight: bold;
  background-color: #333;
  color: #eee;
  font-size: 1em;
  min-width: 0;
  cursor: pointer;
}
svg {
  vertical-align: bottom;
}

:is(input, button):active,
:is(input, button):focus {
  outline: none;
}

.ripple {
  background-position: center;
  transition: background 0.5s;
}
.ripple:hover {
  background: #333 radial-gradient(circle, transparent 1%, #555 1%)
    center/15000%;
}
.ripple:active {
  background-color: #333;
  background-size: 100%;
  transition: background 0s;
}

@keyframes shake {
  10%,
  90% {
    transform: translate3d(-1px, 0, 0);
  }

  20%,
  80% {
    transform: translate3d(2px, 0, 0);
  }

  30%,
  50%,
  70% {
    transform: translate3d(-4px, 0, 0);
  }

  40%,
  60% {
    transform: translate3d(4px, 0, 0);
  }
}
</style>