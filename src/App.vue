npm <template>
  <div>
    <SearchBar v-on:new-input="fetchData" /><SearchResults :prop="results" />
  </div>
</template>

<script>
import SearchBar from "./components/SearchBar.vue";
import SearchResults from "./components/SearchResults.vue";

export default {
  name: "App",
  components: {
    SearchBar,
    SearchResults,
  },

  data: function () {
    return {
      results: ""
    }
  },
  
  computed: {
    api: () => process.env.VUE_APP_API_KEY,

    cx: () => "partner-pub-8353708690493468:1896422876",
  },
  methods: {
    async fetchData(input) {
      console.log("fired fetchData", "input", input);

      const uri = `https://www.googleapis.com/customsearch/v1?key=${this.api}&cx=${this.cx}&q=${input}`;
      console.log(uri);
      const response = await fetch(uri);
      const data = await response.json();
      this.results = data.items
      console.log(data)
    },
  },
};
</script>

