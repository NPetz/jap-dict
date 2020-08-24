<template>
  <main>
    <ol>
      <li v-for="(entry, index) in prop" :key="index">
        <p @click="$emit('fetch-definition', entry)" target="__blank">
          {{ parseResults(entry.title) }}
        </p>
      </li>
    </ol>

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
