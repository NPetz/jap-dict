<template>
  <form v-on:submit.prevent="$emit('new-input', $event.target[0].value)">
    <input type="text" />
    <button type="submit">Search</button>
  </form>
</template>

<script>

export default {
  name: "SearchBar",
};
</script>
