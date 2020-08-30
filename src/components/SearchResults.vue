<template>
  <main>
    <ul id="hits-container" v-if="prop">
      <li v-for="(entry, index) in prop" :key="index" class="hits">
        <p
          @click="$emit('fetch-definition', entry);toggleActive($event)"
          target="__blank"
          class="kanji"
        >
          {{ parseResults(entry.title).text }}
        </p>
        <p class="yomi">{{ parseResults(entry.title).yomi[1] }}</p>
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
      const endString = /とは\s-\s.*/g;
      const inParenthesis = /\(([^\s]*)?\)/;
      text = text.replace(endString, "");

      console.log("stripped of to ha", text);
      const yomi = text.match(inParenthesis);

      console.log("yomi", yomi);

      text = text.replace(/\(.*/, "").replace(/\s*/g,"")

      return { text: text, yomi: yomi ? yomi : ["", text] };
    },
        async toggleActive(e) {
      console.log('fired ToggleActive',e)
      let hits = await document.querySelectorAll('.hits');
      await console.log(hits)
      await hits.forEach((x) => {x.classList.remove("active")});

      e.target.parentElement.classList.add("active");
    },
  },
};
</script>
