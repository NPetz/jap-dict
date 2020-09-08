<template>
  <main>
    <ul id="hits-container" v-if="prop">
      <li v-for="(entry, index) in prop" :key="index" class="hits">
        <p
          @click="
            $emit('fetch-definition', entry);
            toggleActive($event);
          "
          target="__blank"
          class="kanji"
        >
          {{ parseResults(entry.title).text }}
        </p>
        <p class="yomi">{{ parseResults(entry.title).yomi }}</p>
      </li>
    </ul>
    <slot></slot>
  </main>
</template>

<script>
export default {
  name: "SearchResults",
  props: ["prop"],
  methods: {
    parseResults(text) {
      const endString = /とは\s-\s.*/g;
      const inParenthesis = /\(([^\s]*)?\)/;
      text = text.replace(endString, "");

      let yomi = text.match(inParenthesis);

      yomi = yomi ? yomi[1] : "";

      text = text.replace(/\(.*/, "").replace(/\s*/g, "");

      return { text: text, yomi: yomi ? yomi : text };
    },

    async toggleActive(e) {
      let hits = await document.querySelectorAll(".hits");
      await hits.forEach((x) => {
        x.classList.remove("active");
      });

      e.target.parentElement.classList.add("active");
    },
  },
};
</script>
