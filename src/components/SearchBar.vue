<template>
  <form v-on:submit.prevent="readInput">
    <input type="text" ref="inputBar" />
    <button type="submit">Search</button>
  </form>
</template>

<script>

export default {
  name: "SearchBar",

  computed: {
    api: () => process.env.VUE_APP_API_KEY,

    cx: () => "partner-pub-8353708690493468:1896422876",
  },

  methods: {
    readInput() {
      const input = this.$refs.inputBar.value;

      console.log("...reading input", input);

      this.fetchData(input);
    },

    async fetchData(input) {
      console.log("fired fetchData");

      const uri = `https://www.googleapis.com/customsearch/v1?key=${this.api}&cx=${this.cx}&q=${input}`;
      console.log(uri);
      const response = await fetch(uri);
      const data = await response.json();
      console.log(data);
    },
  },
};
</script>
