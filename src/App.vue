<template>
  <div id="container">
    <SearchBar v-on:new-input="fetchData" />
    <SearchResults :prop="results" @fetch-definition="fetchDefinition">

      <section id="results">

        <section id="japRes">

          <h2 v-if="japRes" class="collapse"  @click="(e)=>e.target.parentNode.querySelectorAll('article').forEach((x)=>{(x.style.display == 'none') ?  x.style.display = 'block' : x.style.display = 'none'})" >日本語</h2>

          <article
            v-for="(entry, index) in japRes"
            :key="index"
            class="definition"
            v-html="entry.innerHTML"
          ></article>

        </section>


        <section id="engRes">

          <h2 v-if="engRes" class="collapse">English</h2>

          <article
            v-for="(entry, index) in engRes"
            :key="-index - 1"
            class="definition"
            v-html="entry.innerHTML"
          ></article>

        </section>

      </section>
    </SearchResults>
  </div>
</template>

<script>
import SearchBar from "./components/SearchBar.vue";
import SearchResults from "./components/SearchResults.vue";

import "./assets/css/main.css";

import * as firebase from "firebase/app";
import "firebase/analytics";

const firebaseConfig = {
  apiKey: process.env.VUE_APP_API_KEY,
  authDomain: "adroit-autumn-237308.firebaseapp.com",
  databaseURL: "https://adroit-autumn-237308.firebaseio.com",
  projectId: "adroit-autumn-237308",
  storageBucket: "adroit-autumn-237308.appspot.com",
  messagingSenderId: "411441913058",
  appId: "1:411441913058:web:7ddf2db626f6a036df92bb",
  measurementId: "G-2K44H693HQ",
};

firebase.initializeApp(firebaseConfig);

const parser = new DOMParser();

export default {
  name: "App",
  components: {
    SearchBar,
    SearchResults,
  },

  data: function() {
    return {
      results: null,
      japRes: null,
      engRes: null,
    };
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
      this.results = data.items;
      console.log(data);
    },

    async fetchDefinition(e) {
      await Promise.all([this.fetchJap(e.link), this.fetchEng(e.title)]);
    },

    async fetchJap(url) {
      console.log("fetching definitionfrom:", url);

      const response = await fetch(
        `https://api.allorigins.win/get?url=${encodeURIComponent(url)}`
      );
      const data = await response.json();
      const htmlData = parser
        .parseFromString(data.contents, "text/html")
        .getElementById("mainArea")
        .querySelectorAll("article .description");
      this.japRes = htmlData ? htmlData : "No Results";
      console.log(data, this.japRes);
    },

    async fetchEng(term) {
      console.log("fethcing englsih defnitnon", term);
      let match = null;

      try {
        match = term.match(/(.*)\(/i)[1];
      } catch (e) {
        console.log("no match", e);
      }
      term = match ? match : term.replace(/とは\s-\s.*/g, "");
      console.log("fetching for term:", term);
      const uri = await `http://nihongo.monash.edu/cgi-bin/wwwjdic?1MUJ${encodeURIComponent(
        term
      )}`;
      const response = await fetch(
        `https://api.allorigins.win/get?url=${encodeURIComponent(uri)}`
      );
      const data = await response.json();

      const htmlData = parser
        .parseFromString(data.contents, "text/html")
        .querySelectorAll("div[style='clear: both;']");

      this.engRes = htmlData ? htmlData : "No Results";

      console.log("parsed value: ", htmlData);
    },
  },
};
</script>
