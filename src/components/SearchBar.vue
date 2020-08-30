<template>
  <form v-on:submit.prevent="$emit('new-input', $event.target[0])" id="form">
    <input type="text" placeholder="look up a Japanese word" id="input" />
    <button type="submit">Search</button>
  </form>
</template>

<script>


window.addEventListener("DOMContentLoaded", () => {
  document.getElementById("input").addEventListener("transitionend", (e) => {
    e.target.style.background = "white";
  });
});

export default {
  name: "SearchBar",
};
</script>
