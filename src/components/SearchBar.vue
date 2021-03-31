


    <!-- v-on:submit.prevent="$emit('new-input', $event.target[0])" -->
<template>
  <form
    id="form"
    v-bind:class="{ firstVisit: firstTime, shake: shake }"
    v-on:submit.prevent="
      onSubmit($event);
      $emit('search', $event.target[0].value);
    "
  >
    <input type="text" placeholder="日本語か英語の言葉" id="input" />
    <button class="ripple" type="submit">検索</button>
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
      } else {
        this.shake = true;
        setTimeout(() => (this.shake = false), 1000);
      }
    },
  },
};
</script>

<style scoped>
form {
  display: flex;
  margin: 0 auto;
  box-shadow: 0 0 1px #555;
  font-family: "Montserrat", "Noto Serif JP";
  font-size: 1.2rem;
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
  padding: 1rem;
  border: none;
  font-family: "Montserrat", "Noto Serif JP";
  font-size: 1em;
  min-width: 0;
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
}

:is(input, button):active,
:is(input, button):focus {
  outline: none;
  border: none;
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