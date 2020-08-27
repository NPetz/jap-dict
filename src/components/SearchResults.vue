<template>
  <main>
    <ul id="hits-container">
      <li v-for="(entry, index) in prop" :key="index" class="hits">
        <p @click="$emit('fetch-definition', entry)" target="__blank">
          {{ parseResults(entry.title) }}
        </p>
      </li>
    </ul>

    <section>
      <slot></slot>
    </section>
  </main>
</template>

<script>
export default {
  name: "SearchResults",
  props: ["prop"],
  methods: {
    parseResults(text) {
      console.log("original text = ", text);
      //
      const endString = /とは\s-\s.*/g;

      return text.replace(endString, "");
    },
  },
};
</script>
